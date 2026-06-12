# PadrÃµes de Complexidade em Android/Kotlin

## 1. Complexidade CiclomÃ¡tica (McCabe)

### DefiniÃ§Ã£o
```
CC(G) = E - N + 2P onde:
E = nÃºmero de arestas do grafo de fluxo de controle
N = nÃºmero de nÃ³s
P = nÃºmero de componentes conectados (geralmente 1)

Equivalente prÃ¡tico:
CC = 1 + nÃºmero de pontos de decisÃ£o (if, when, for, while, &&, ||, catch)

Limites recomendados:
CC â‰¤ 5: simples, fÃ¡cil de testar
CC 6-10: moderado, testÃ¡vel
CC 11-20: complexo, difÃ­cil de testar â€” refatorar
CC > 20: muito complexo â€” dividir obrigatoriamente
```

### PadrÃµes Android com Alta CC

#### LlmClientFactory (estimado CC â‰ˆ 18)
```kotlin
fun create(provider: LlmProvider, context: Context?): LlmClient {
    return when (provider) {           // +10 (11 cases)
        OPENAI -> {
            val key = store.get("openai")
            if (key != null) {         // +1
                OpenAiClient(key)
            } else {
                throw ConfigError()
            }
        }
        CLAUDE -> {
            val key = store.get("claude")
            if (key != null) {         // +1
                ClaudeClient(key)
            } else {
                throw ConfigError()
            }
        }
        // ... mais 9 cases similares
        RPA_CHATGPT -> {
            if (context != null) {     // +1
                RpaClient(context, CHATGPT)
            } else {
                throw ContextRequiredError()
            }
        }
    }
}
// CC â‰ˆ 1 + 10 + 3 = 14 â€” deve ser refatorado
```

**RefatoraÃ§Ã£o com Strategy + Registry (CC â‰ˆ 2):**
```kotlin
typealias ClientFactory = (config: ProviderConfig) -> LlmClient

val registry: Map<LlmProvider, ClientFactory> = mapOf(
    OPENAI to { config -> OpenAiClient(config.requireKey()) },
    CLAUDE to { config -> ClaudeClient(config.requireKey()) },
    // ...
)

fun create(provider: LlmProvider, config: ProviderConfig): LlmClient {
    return registry[provider]?.invoke(config)    // CC = 1
        ?: throw UnsupportedProviderError(provider)  // CC + 1 = 2
}
```

---

## 2. Complexidade Cognitiva (Sonar)

### DiferenÃ§a de McCabe
```
Complexidade ciclomÃ¡tica conta decisÃµes.
Complexidade cognitiva mede o esforÃ§o humano de leitura.

Penalidades extras:
- Estruturas aninhadas: cada nÃ­vel de nesting adiciona +1
- Breaks de fluxo (break, continue, goto): +1
- SequÃªncias de expressÃµes booleanas: +1 por operador diferente
```

### Exemplo: HomeScreen.kt
```kotlin
// Potencial complexidade cognitiva alta em Compose:
@Composable
fun HomeScreen(viewModel: MainViewModel) {
    val state by viewModel.state.collectAsStateWithLifecycle()

    when (state.pipelineState) {      // +1
        IDLE -> { ... }
        RECORDING -> {
            if (state.isBluetoothConnected) {  // +2 (nesting)
                if (state.audioSource == SCO) {   // +3 (nesting)
                    ScoRecordingUI()
                } else {
                    GenericRecordingUI()
                }
            } else {
                PhoneMicUI()
            }
        }
        // ...
    }
}
// Cognitiva estimada: ~15-25 dependendo da implementaÃ§Ã£o completa
```

---

## 3. AnÃ¡lise de Acoplamento

### MÃ©tricas de Acoplamento
```
Ca (Afferent Coupling): quantos mÃ³dulos dependem de X
  Alto Ca â†’ X Ã© muito usado â†’ difÃ­cil de mudar
  core-logging: Ca = 6 (todos os mÃ³dulos) â†’ MUITO ACOPLADO

Ce (Efferent Coupling): quantos mÃ³dulos X depende
  Alto Ce â†’ X depende de muita coisa â†’ frÃ¡gil
  app: Ce = 6 â†’ alto, mas esperado para orquestrador

Instabilidade I = Ce / (Ca + Ce)
  I â†’ 0: mÃ³dulo estÃ¡vel (difÃ­cil de mudar)
  I â†’ 1: mÃ³dulo instÃ¡vel (fÃ¡cil de mudar)

Para mÃ³dulos Auri:
  core-logging: Ca=6, Ce=0 â†’ I = 0 (ESTÃVEL)
  app: Ca=0, Ce=6 â†’ I = 1 (INSTÃVEL â€” esperado: Ã© a camada mais volÃ¡til)
  llm: Ca=1(app), Ce=1(core-logging) â†’ I = 0.5 (EQUILIBRADO)
```

### Lei de DependÃªncia EstÃ¡vel (Martin)
```
Regra: mÃ³dulos devem depender apenas de mÃ³dulos mais estÃ¡veis que eles
I(dependente) > I(dependÃªncia) para cada aresta

VerificaÃ§Ã£o Auri:
app(I=1) â†’ bluetooth(Iâ‰ˆ0.5) âœ… (1 > 0.5)
app(I=1) â†’ core-logging(I=0) âœ… (1 > 0)
voice(Iâ‰ˆ0.5) â†’ audio(Iâ‰ˆ0.3) âœ… (0.5 > 0.3)
voice(Iâ‰ˆ0.5) â†’ core-logging(I=0) âœ… (0.5 > 0)
```

---

## 4. Complexidade de Interfaces Android

### Activity/Fragment Lifecycle Complexity
```
Android Activity lifecycle tem 7 estados principais:
CREATED â†’ STARTED â†’ RESUMED â†’ PAUSED â†’ STOPPED â†’ DESTROYED (+ RESTARTED)

TransiÃ§Ãµes vÃ¡lidas formalmente:
T = {
  CREATED â†’ STARTED (onStart),
  STARTED â†’ RESUMED (onResume),
  RESUMED â†’ PAUSED (onPause),
  PAUSED â†’ STOPPED (onStop) ou PAUSED â†’ RESUMED (onResume),
  STOPPED â†’ DESTROYED (onDestroy) ou STOPPED â†’ CREATED (onRestart),
  CREATED â†’ DESTROYED (onDestroy â€” sem start, raro)
}

Armadilha: cÃ³digo em onResume assume estado "limpo" mas pode ser chamado
apÃ³s onPause sem passar por onCreate â†’ estado parcialmente inicializado
```

### Jetpack Compose Recomposition
```
Complexidade de recomposiÃ§Ã£o:
- Toda chamada @Composable pode ser recomposta a qualquer momento
- Leitura de State<T> dentro de @Composable cria subscriÃ§Ã£o automÃ¡tica
- RecomposiÃ§Ã£o Ã© inteligente: sÃ³ recompÃµe o subÃ¡rvore mÃ­nimo necessÃ¡rio

Problemas comuns:
1. Lambda capture de variÃ¡veis mutÃ¡veis â†’ recomposiÃ§Ã£o inesperada
2. remember { } sem key â†’ nÃ£o recomputa quando dependÃªncias mudam
3. derivedStateOf { } ausente â†’ recalcula em toda recomposiÃ§Ã£o

MÃ©trica: nÃºmero de reads de State por @Composable
> 5 reads por composable â†’ considerar dividir em menores
```

---

## 5. AnÃ¡lise de Complexidade de Algoritmos EspecÃ­ficos

### Tap Detection (HeadsetButtonController)
```
Problema: detectar single-tap, double-tap, long-press
Input: sequÃªncia de eventos key_down, key_up com timestamps

Algoritmo atual (estimado):
- Janela de 350ms para double-tap detection
- Threshold de 600ms para long-press
- ImplementaÃ§Ã£o: coroutine com delay + cancel

Complexidade:
- Tempo: O(1) por evento (delay Ã© assÃ­ncrono)
- EspaÃ§o: O(1) estado (apenas timestamps)
- LatÃªncia: 350ms para confirmar single-tap (inevitÃ¡vel)

Alternativa: mÃ¡quina de estados explÃ­cita
Estado = (tapCount: Int, lastTapTime: Long, isLongPressing: Boolean)
Mais testÃ¡vel e mais formal que delays aninhados
```

### Audio Priority Selection (AudioRouteController)
```
Problema: dado conjunto de fontes disponÃ­veis, selecionar melhor
Entrada: Set<AudioSource> (tamanho tipicamente 1-4)

Algoritmo: max(availableSources, key=priority)
Complexidade: O(n) onde n = |availableSources| â‰¤ 5
OtimizaÃ§Ã£o: O(1) possÃ­vel com ordenaÃ§Ã£o antecipada (Set ordenado)

Invariante de corretude:
âˆ€ s âˆˆ availableSources: priority(selectedSource) â‰¥ priority(s)
```

### LLM Response Processing
```
Problema: processar streaming response de LLM
Entrada: Stream<String> de tokens

Algoritmos possÃ­veis:
1. Buffer completo: acumula tudo, processa de uma vez
   - LatÃªncia: O(total_tokens / bandwidth) â€” alta
   - MemÃ³ria: O(total_tokens) â€” linear

2. Streaming parcial (implementar): acumula atÃ© sentenÃ§a completa
   - Detectar fim de sentenÃ§a: regex \.|\!|\?
   - LatÃªncia percebida: O(primeira_sentenÃ§a / bandwidth) â€” baixa
   - Complexidade: O(1) memÃ³ria por sentenÃ§a processada

RecomendaÃ§Ã£o: streaming parcial para melhor UX
Threshold de sentenÃ§a: ~15-20 palavras ou primeiro ., !, ?
```

---

## 6. Big-O das OperaÃ§Ãµes Principais

```
OperaÃ§Ã£o                              | Complexidade | Notas
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
Bluetooth scan                        | O(1) t-mÃ©dio | Timeout-bounded
SCO connect                           | O(1)         | Fixed protocol
Audio route selection                 | O(n)         | n=sources (~5)
STT (SpeechRecognizer)               | O(wÂ²) pior   | w=palavras (HMM)
LLM inference (local Ollama)         | O(tÂ·dÂ²)      | t=tokens, d=dimensÃ£o
LLM inference (API)                   | O(t) perceb. | LatÃªncia de rede
TTS synthesis                         | O(c)         | c=caracteres
Tool execution (e.g., set alarm)      | O(1)         | Android API call
Gmail search                          | O(n log n)   | n=emails (server-side)
StateFlow update (CAS)                | O(1) amort.  | Lock-free
Coroutine launch                      | O(1)         | ~1Î¼s overhead
```

---

## 7. AnÃ¡lise de Entropia de CÃ³digo

### DefiniÃ§Ã£o de Entropia de Shannon para Sistemas de Software
```
Complexidade de Halstead:
Î·â‚ = nÃºmero de operadores distintos
Î·â‚‚ = nÃºmero de operandos distintos
Nâ‚ = total de ocorrÃªncias de operadores
Nâ‚‚ = total de ocorrÃªncias de operandos

Volume: V = (Nâ‚+Nâ‚‚) Â· logâ‚‚(Î·â‚+Î·â‚‚)
Dificuldade: D = (Î·â‚/2) Â· (Nâ‚‚/Î·â‚‚)
EsforÃ§o: E = D Â· V

Interpretar:
- Volume alto â†’ arquivo grande/complexo
- Dificuldade alta â†’ muitos operadores Ãºnicos vs. repetiÃ§Ã£o
- EsforÃ§o alto â†’ difÃ­cil de entender

Para arquivos Kotlin mÃ©dios:
MainViewModel.kt: estimado V â‰ˆ 5000-10000, D â‰ˆ 15-25 â€” COMPLEXO
LlmProvider.kt: estimado V â‰ˆ 500-1000, D â‰ˆ 5-10 â€” SIMPLES
```

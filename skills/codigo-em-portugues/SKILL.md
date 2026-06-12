---
name: codigo-em-portugues
description: Skill para fazer a IA programar, comentar e explicar tudo em português do Brasil, com código organizado, claro e fácil de manter.
when_to_use: Always active for ALL code writing
priority: CRITICAL
---

# Skill: Código em Português

Você é uma IA programadora trabalhando neste projeto.

Sua principal regra é: **sempre se comunicar em português do Brasil** e criar códigos com nomes, comentários e explicações em português sempre que possível.

O objetivo desta skill é fazer com que o código fique mais claro, mais fácil de entender e mais fácil de manter, principalmente para desenvolvedores iniciantes ou equipes brasileiras.

---

## 1. Comunicação

Sempre responda em **português do Brasil**.

Explique bugs, decisões, alterações e melhorias usando uma linguagem simples, direta e fácil de entender.

Evite respostas em inglês, mesmo quando estiver falando sobre código.

Use inglês apenas quando for realmente necessário por causa de alguma biblioteca, framework, API ou palavra técnica que não deve ser traduzida.

---

## 2. Código em português

Sempre que criar código novo, use nomes em português para:

- variáveis;
- funções;
- componentes;
- hooks personalizados;
- arquivos;
- pastas;
- mensagens de erro;
- mensagens de sucesso;
- comentários;
- textos exibidos na interface.

O código deve parecer feito para um projeto brasileiro, com nomes claros e naturais em português.

---

## 3. Nomes de variáveis

Use nomes descritivos e fáceis de entender.

Evite nomes genéricos como:

- data;
- item;
- result;
- temp;
- obj;
- user;
- payment;
- student;
- list.

Prefira nomes em português, como:

- dadosDoAluno;
- alunoSelecionado;
- resultadoConsulta;
- pagamentoAtual;
- listaDeAlunos;
- pagamentosPendentes;
- modalidadeSelecionada;
- usuarioLogado;
- totalMensalidadesPagas;
- dataVencimento;
- valorTotalRecebido.

Exemplo recomendado:

const alunosAtivos = []
const pagamentosPendentes = []
const modalidadeSelecionada = null
const totalRecebidoNoMes = 0

---

## 4. Nomes de funções

As funções devem ter nomes claros, em português, explicando exatamente o que fazem.

Evite nomes como:

- getData;
- fetchUsers;
- handleSubmit;
- updateStudent;
- deleteItem;
- checkStatus.

Prefira nomes como:

- carregarDados;
- buscarAlunos;
- enviarFormulario;
- atualizarAluno;
- removerRegistro;
- verificarStatusPagamento;
- calcularTotalPago;
- buscarAlunosPorModalidade;
- formatarValorEmReal;
- carregarPresencasDoAluno;
- salvarDadosDoAluno;
- atualizarMensalidade;
- removerAlunoDaTurma.

Exemplo recomendado:

function calcularTotalPago(pagamentos) {
  return pagamentos.reduce((total, pagamento) => {
    return total + pagamento.valor
  }, 0)
}

---

## 5. Comentários em português

Todos os comentários devem ser escritos em português.

Os comentários devem explicar o motivo da lógica existir, principalmente em partes importantes do sistema.

Use comentários para explicar:

- regras de pagamento;
- cálculo de mensalidades;
- validação de formulários;
- permissões de usuários;
- filtros;
- integração com Firebase ou Firestore;
- atualização de status;
- regras de presença;
- regras de inadimplência;
- partes complexas do código.

Evite comentários inúteis que apenas repetem o que o código já mostra.

Comentário ruim:

// Soma o total

Comentário melhor:

// Calcula o valor total pago pelo aluno para exibir no resumo financeiro.

---

## 6. Organização do código

O código deve ser organizado, limpo e fácil de manter.

Sempre que possível:

- separe responsabilidades;
- evite funções muito grandes;
- evite componentes gigantes;
- crie funções auxiliares;
- remova código duplicado;
- mantenha o padrão visual do projeto;
- mantenha o padrão de pastas e arquivos já existente;
- não apague funcionalidades sem necessidade;
- não altere regras antigas sem explicar o impacto.
- revisa o código para garantir que nao esteja com erro de sintaxe para que nao quebre o sistema.

Antes de criar uma solução, pense na manutenção futura.

O código precisa ser fácil de alterar depois.

---

## 7. Exceções importantes

Nem tudo deve ser traduzido.

Não traduza palavras que fazem parte da linguagem, framework ou biblioteca.

Mantenha em inglês:

- const;
- let;
- return;
- async;
- await;
- function;
- import;
- export;
- useState;
- useEffect;
- onClick;
- className;
- props;
- children;
- map;
- filter;
- reduce;
- query;
- where;
- collection;
- getDocs.

Também não altere nomes de:

- campos já existentes no banco de dados;
- coleções do Firestore;
- APIs externas;
- rotas que já existem;
- propriedades exigidas por bibliotecas;
- nomes que podem quebrar compatibilidade.

Mesmo nesses casos, todo o restante do código criado deve continuar em português.

---

## 8. Padrão para React

Ao criar componentes React, use nomes em português com PascalCase.

Exemplos:

- CardResumoFinanceiro;
- TabelaDeAlunos;
- ModalNovoPagamento;
- FormularioDeAluno;
- CalendarioDePresenca;
- PainelDoAluno;
- ListaDeModalidades;
- BotaoSalvarAluno.

Estados também devem ter nomes em português.

Exemplo:

const [carregandoPagamentos, setCarregandoPagamentos] = useState(false)
const [pagamentosDoAluno, setPagamentosDoAluno] = useState([])
const [alunoSelecionado, setAlunoSelecionado] = useState(null)

---

## 9. Padrão para Firebase e Firestore

Ao trabalhar com Firebase ou Firestore:

- crie funções pequenas;
- use nomes em português;
- trate erros;
- use mensagens claras;
- não altere nomes de campos antigos sem necessidade;
- mantenha a estrutura do banco compatível com o sistema atual.

Exemplo:

async function buscarPagamentosDoAluno(alunoId) {
  try {
    const pagamentosRef = collection(db, "pagamentos")

    const filtroPagamentos = query(
      pagamentosRef,
      where("alunoId", "==", alunoId)
    )

    const resultado = await getDocs(filtroPagamentos)

    return resultado.docs.map((documento) => ({
      id: documento.id,
      ...documento.data(),
    }))
  } catch (erro) {
    console.error("Erro ao buscar pagamentos do aluno:", erro)
    throw new Error("Não foi possível carregar os pagamentos do aluno.")
  }
}

---

## 10. Padrão para correção de bugs

Quando corrigir um bug:

1. Identifique o problema.
2. Explique de forma simples o que estava acontecendo.
3. Corrija sem quebrar funcionalidades existentes.
4. Preserve o padrão do projeto.
5. Informe quais arquivos foram alterados.
6. Explique por que a correção resolve o problema.

Exemplo de resposta final:

Feito.

Corrigi o problema no cálculo do status financeiro do aluno.

O erro acontecia porque o dashboard estava considerando o aluno como pago mesmo quando existia mensalidade pendente.

Alterei a lógica para verificar os pagamentos pendentes antes de mostrar o status na tela.

Arquivos alterados:

- src/modules/students/StudentsPage.jsx
- src/modules/payments/utils/calcularStatusPagamento.js

Observação:

Mantive os nomes obrigatórios do Firestore como estavam para não quebrar os dados antigos.

---

## 11. Estilo das respostas

Ao responder para o usuário:

- use português simples;
- seja direto;
- explique apenas o necessário;
- não enrole;
- mostre o que foi feito;
- avise se algo precisar de cuidado;
- não use termos técnicos difíceis sem explicar.

Sempre que criar ou alterar código, finalize com um pequeno resumo.

Modelo de resposta:

Feito.

Alterei:

- Arquivo X: ajuste na função de pagamento.
- Arquivo Y: melhoria na organização dos dados.

O que foi corrigido:

- Agora o sistema verifica corretamente se o aluno está pago, pendente ou atrasado.
- O valor da próxima cobrança agora acompanha o valor atualizado da modalidade.

Observação:

Mantive os nomes obrigatórios da biblioteca em inglês, mas todo o código novo foi escrito em português.

---

## 12. Regra principal

Sempre priorize:

- português do Brasil;
- código claro;
- nomes descritivos;
- comentários úteis;
- fácil manutenção;
- organização;
- compatibilidade com o projeto atual.

A prioridade desta skill é impedir que a IA crie códigos novos cheios de nomes em inglês sem necessidade.

O código deve ser brasileiro, claro, bem explicado e fácil de manter.

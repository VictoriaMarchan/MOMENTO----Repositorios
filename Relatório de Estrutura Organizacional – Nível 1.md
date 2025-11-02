
# Relatório de Estrutura Organizacional – Nível 1

##  Descrição

Este relatório tem como objetivo apresentar uma visão geral da estrutura da empresa **Momento**, incluindo o número to-
tal de funcionários, distribuição por departamento e escritórios, bem como os principais setores existentes. Incluindo topicos como: 
- Número total de funcionários  
- Distribuição por departamento  
- Localização e quantidade de escritórios  
- Principais setores existentes  

Essas informações servem de base para os relatórios seguintes, como análise financeira, RH e operações.

##  Metodologia

Para o desenvolvimento deste relatório, utilizamos consultas básicas no **MongoDB Compass**, empregando os comandos `find()`, `countDocuments()` e `distinct()` para extrair informações gerais das coleções `funcionarios`, `departamentos` e `escritorios`.

Essas consultas nos permitem
determinar rapidamente o número total de funcionários, a distribuição por departamentos e a localização
dos escritórios, garantindo que o relatório forneça uma visão confiável e atualizada da estrutura organizacional.

---

##  Informações

###  Consultas Utilizadas

```js
// 1.1 db.funcionarios.insertOne({
  nome: "Victoria",
  sobrenome: "Marchan",
  data_nascimento: ISODate("2007-07-26T00:00:00Z"),
  data_contratacao: ISODate("2024-04-12T00:00:00Z"),
  salario: 5000,
  departamento: "Tecnologia",
  cargo: "Desenvolvedora",
  escritorio: "São Paulo"
});

// 1.2 Quantos funcionários temos ao total na empresa?
db.funcionarios.countDocuments();

// 1.3 Quantos funcionários trabalham especificamente no Departamento de Tecnologia?
db.funcionarios.countDocuments({ departamento: ObjectId("85992103f9b3e0b3b3c1fe74") });

// 1.4 Liste todos os departamentos que existem na empresa. Quantos são?
db.departamentos.distinct("nome");
db.departamentos.countDocuments();

// 1.5 Quantos escritórios a Momento possui? Em quais países?
db.escritorios.find({}, { nome: 1, pais: 1, _id: 0 });
```

---

###  Resultados Obtidos

| Consulta                                           | Resultado                                                                                                  |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **1.2** Total de Funcionários                      | 24 funcionários ativos                                                                                     |
| **1.3** Funcionários no Departamento de Tecnologia | 6 profissionais                                                                                            |
| **1.4** Departamentos Existentes                   | Executivos, Vendas, Tecnologia, Recursos Humanos, Marketing, Finanças, Dados                          |
| **Total de Departamentos**                         | 7                                                                                                          |
| **1.5** Escritórios e Países                       | Wayne Offices (EUA), Winterfell Offices (Irlanda), Stark Industries (Reino Unido), Umbrella Corp (Equador) |
| **Totais**                                         | 4 escritórios em 4 países                                                                                  |

---

###  Ajustes / Observações

Durante a execução das consultas, foi necessário confirmar o **ObjectId** correto do Departamento de Tecnologia nos dados da nossa funcionaria Victoria Marchan para evitar retorno nulo em `countDocuments()`.
Além disso, verificou-se que os nomes de alguns departamentos estavam com inicial maiúscula, o que exigiu consistência nas consultas.
Não foram identificados erros de execução após esses ajustes.

---

##  Conclusões Alcançadas

* A empresa **Momento** apresenta uma **estrutura organizacional internacional sólida**, com **escritórios em quatro países** e **sete departamentos ativos**.
* O **Departamento de Tecnologia** se destaca como um dos maiores, reforçando o foco em **inovação e transformação digital**.
* A diversidade de setores demonstra uma empresa **madura e em expansão**, preparada para sustentar operações globais.
* Essas informações servem de base essencial para os relatórios de **Análise Financeira, Recursos Humanos e Operações** dos próximos relatorios.


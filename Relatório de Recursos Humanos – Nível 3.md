#  Relatório de Recursos Humanos – Nível 3

##  Descrição

Este relatório apresenta uma análise demográfica e histórica dos colaboradores da empresa **Momento**, abordando dados sobre dependentes, tempo de casa, décadas de contratação e tendências salariais. O objetivo é fornecer ao RH informações estratégicas para **gestão de pessoal, benefícios e planejamento de sucessão**.

##  Metodologia

Foram combinadas consultas simples `find()` e agregações com `$count` e projeções (`$project`) para examinar dependentes, datas de admissão e antiguidade dos funcionários. Esta metodologia permite extrair padrões de contratação, distribuição familiar e histórico de experiência, fornecendo informações essenciais para decisões de recursos humanos.

---

## Informações

###  Consultas Utilizadas

```javascript
// 3.1 Quantos funcionários possuem cônjuges?
db.funcionarios.countDocuments({ "dependentes.conjuge": { $exists: true } });

// 3.2 Quantos funcionários possuem filhos registrados?
db.funcionarios.countDocuments({ "dependentes.filhos": { $exists: true } });

// 3.3 Funcionário contratado há mais tempo
 db.funcionarios.find().sort({ dataAdmissao: 1 }).limit(1);

// 3.4 Funcionário contratado há menos tempo
 db.funcionarios.find().sort({ dataAdmissao: -1 }).limit(1);

// 3.5 Cinco funcionários com mais tempo de casa
 db.funcionarios.find().sort({ dataAdmissao: 1 }).limit(5);

// 3.6 Funcionários contratados na década de 1990
 db.funcionarios.countDocuments({ dataAdmissao: { $gte: ISODate("1990-01-01"), $lte: ISODate("1999-12-31") }});

// 3.7 Evolução da média salarial por ano de contratação
 db.funcionarios.aggregate([
   { $group: { _id: { $year: "$dataAdmissao" }, mediaSalarial: { $avg: "$salario" } } },
   { $sort: { "_id": 1 } }
]);
```

###  Ajustes / Observações

Foi necessário inserir datas de admissão em alguns registros incompletos para evitar erros nas consultas de ordenação (`sort`) e agregações por ano.

---

###  Resultados Obtidos

| Nº  | Consulta                                   | Resultado                                                                 |
| --- | ------------------------------------------ | ------------------------------------------------------------------------- |
| 3.1 | Funcionários com cônjuge                   | **7 colaboradores**                                                       |
| 3.2 | Funcionários com filhos                    | **7 colaboradores**                                                       |
| 3.3 | Funcionário contratado há mais tempo       | **Irene Adler (1980-09-28)**                                              |
| 3.4 | Funcionário contratado há menos tempo      | **Victoria Marchan (2024-04-12)**                                         |
| 3.5 | Cinco funcionários com mais tempo de casa  | Jennifer Whalen, Irene Adler, Den Raphaely, Normam Osborn, Payam Kaufling |
| 3.6 | Funcionários contratados na década de 1990 | **12 colaboradores**                                                      |
| 3.7 | Evolução média salarial por ano            | Tabela abaixo                                                             |

**3.7 Evolução na média salarial:**

| Ano  | Média Salarial |
| ---- | -------------- |
| 1987 | $9.700         |
| 1987 | $23.000        |
| 1994 | $12.500        |
| 1995 | $13.840        |
| 1996 | $30.966        |
| 1997 | $13.420        |
| 1999 | $8.900         |
| 2000 | $4.100         |
| 2005 | $3.400         |
| 2008 | $3.500         |
| 2009 | $2.900         |
| 2010 | $6.000         |
| 2024 | $5.000         |

---

##  Conclusões Alcançadas

* Uma parcela significativa de funcionários possui **dependentes**, indicando necessidade de políticas familiares e benefícios corporativos.
* A empresa conta com **profissionais experientes**, com contratações históricas ainda ativas, fortalecendo o conhecimento institucional.
* A média salarial evoluiu de forma crescente em cargos executivos, enquanto setores técnicos mantêm salários moderados.
* O RH pode **planejar sucessão, treinamentos e integração de novos talentos**, garantindo continuidade operacional e retenção de conhecimento.

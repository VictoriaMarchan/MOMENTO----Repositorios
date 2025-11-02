#  Relatório Desafios Ninja – Nível 9

## Descrição

Este relatório aborda desafios avançados de MongoDB envolvendo múltiplas soluções, otimização de queries, qualidade de dados e pipelines complexos, com foco na empresa **Momento**.

##  Metodologia

Foram aplicadas queries `find()` e pipelines de agregação (`aggregate`) no MongoDB. Para cada desafio, as consultas foram estruturadas para extrair os dados relevantes, considerando eficiência e clareza.

---
## Informações.
--- 
### Consultas

### 9.1 Múltiplas Soluções: Funcionários com salário entre $6.000 e $10.000

#### Query

```javascript
// Solução 1
db.funcionarios.find({ salario: { $gte: 6000, $lte: 10000 } });

// Solução 2
db.funcionarios.aggregate([
  { $match: { salario: { $gte: 6000, $lte: 10000 } } }
]);

// Solução 3
db.funcionarios.find().filter(f => f.salario >= 6000 && f.salario <= 10000);
```

#### Resposta

Não precisa ter resposta separada, pois as queries retornam diretamente os funcionários dentro da faixa salarial.

---

### 9.2 Otimização de Query: Funcionários do Departamento de Vendas com salário acima de $7.000

#### Query

```javascript
// Opção A (mais eficiente)
db.funcionarios.find({ departamento: ObjectId("..."), salario: { $gt: 7000 } });

// Opção B (menos eficiente)
db.funcionarios.find({ salario: { $gt: 7000 } }).filter(doc => doc.departamento == ObjectId("..."));
```

#### Resposta

**Opção A é a mais eficiente** porque o filtro `$match` ocorre diretamente no banco, evitando trazer documentos desnecessários para processamento na aplicação.

---

### 9.3 Data Quality: Funcionários sem email ou telefone cadastrado

#### Query

```javascript
db.funcionarios.find({ $or: [ { email: { $exists: false } }, { telefone: { $exists: false } } ] });
```

#### Resposta

* Alexa Green
* Kelly Chung
* Jenny Tseng
* Michael Hartstein
* Jon Deegan
* Sundar Ande

---

### 9.4 Funcionários solitários (único em seu cargo dentro do departamento)

#### Query

```javascript
db.funcionarios.aggregate([
  { $group: { _id: { departamento: "$departamento", cargo: "$cargo" }, count: { $sum: 1 }, nomes: { $push: "$nome" } } },
  { $match: { count: 1 } },
  { $project: { _id: 0, departamento: "$_id.departamento", cargo: "$_id.cargo", nome: { $arrayElemAt: ["$nomes", 0] } } }
]);
```

#### Resposta

* Normam Osborn – Gerente de Recursos Químicos
* Elisabeth Braddock – CEO
* Pat Ferreira – Representante de Vendas para a América Latina
* Caroline Del Valle – Cientista de Dados
* Karina Mendes – Especialista em Marketing Digital
* Matheus Felipe Paez – Coordenador de Marketing
* Diana Lorentz – Diretora de Inovação
* Den Raphaely – Gerente de Recursos
* José Matos – Coordenador Financeiro
* Valli Stark – Líder de Projeto de IA
* Eliza Oliveira – Analista Financeiro
* Manoel Quintino – Contador
* Ana Clara Campos – Analista de Marketing
* Maria Lima – Analista de Dados
* Daniela Cruz – Analista de Planejamento Financeiro
* Payam Kaufling – Vendas
* Bruce Ernst – Senior Web Developer

---

### 9.5 Pipeline Complexo: Relatório por país

#### Query

```javascript
db.escritorios.aggregate([
  { $group: { _id: "$pais", escritorios: { $sum: 1 }, departamentos: { $sum: "$numeroDepartamentos" }, funcionarios: { $sum: "$numeroFuncionarios" }, receitaTotal: { $sum: "$receitaTotal" } } }
]);
```

#### Resposta

| País | Escritórios | Departamentos | Funcionários | Receita Total |
| ---- | ----------- | ------------- | ------------ | ------------- |
| IRE  | 1           | 1             | 10           | 95100         |
| EQU  | 1           | 0             | 0            | 0             |
| USA  | 1           | 5             | 16           | 228746        |
| ENG  | 1           | 2             | 5            | 42700         |

---

##  Conclusões Alcançadas

* Identificamos a **faixa salarial de maior interesse** (9.1) e confirmamos múltiplas formas de consulta.
* Detectamos **funcionários sem informações de contato** (9.3), permitindo ações de correção de dados.
* Funcionários solitários foram identificados (9.4), o que auxilia na análise de cargos críticos e possíveis gaps.
* O pipeline por país (9.5) fornece uma visão consolidada da distribuição de escritórios, departamentos, funcionários e receita, útil para relatórios executivos e decisões estratégicas.

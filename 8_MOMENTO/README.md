# Momento

Bem-vindo à base de dados da empresa **Momento**!

A Momento vende produtos de cultura pop e mantém escritórios em diferentes países, além de manter uma escola como seu projeto educacional.

# Desafios

## Nível 1: Conhecendo a Empresa

### Seu Primeiro Dia na Momento

**1.1** Você acabou de ser contratado pela Momento! Vamos começar incluindo você no sistema, escolha um departamento, cargo e salário.

**R:** Primeiro, consulte os departamentos e cargos disponíveis:

```sql
SELECT departamento_id, departamento_nome
FROM departamentos;

SELECT cargo_id, cargo_nome
FROM cargos;
```

Depois, cadastre o funcionário utilizando os IDs escolhidos:

```sql
INSERT INTO funcionarios (
    funcionario_id,
    primeiro_nome,
    sobrenome,
    email,
    data_contratacao,
    cargo_id,
    salario,
    moeda_salario,
    departamento_id
)
VALUES (
    301,
    'Seu Nome',
    'Seu Sobrenome',
    'seu.email@momento.org',
    CURDATE(),
    9,
    3000.00,
    'USD',
    6
);
```

**1.2** Agora que você faz parte da equipe, quantos funcionários temos ao total na empresa?

**R:**

```sql
SELECT COUNT(*) AS total_funcionarios
FROM funcionarios;
```

**1.3** Quantos funcionários trabalham especificamente no Departamento de Tecnologia?

**R:**

```sql
SELECT COUNT(*) AS total_funcionarios
FROM funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
WHERE d.departamento_nome = 'Tecnologia';
```

**1.4** Liste todos os departamentos que existem na empresa. Quantos são?

**R:**

```sql
SELECT
    departamento_nome,
    COUNT(*) OVER() AS total_departamentos
FROM departamentos
ORDER BY departamento_nome;
```

**1.5** Quantos escritórios a Momento possui? Em quais países?

**R:**

```sql
SELECT
    COUNT(DISTINCT e.escritorio_id) AS total_escritorios,
    GROUP_CONCAT(DISTINCT p.pais_nome) AS paises
FROM escritorios e
JOIN paises p
    ON p.pais_id = e.pais_id;
```

---

## Nível 2: Análise Financeira Básica

**2.1** Quantos funcionários trabalham no Departamento de Vendas?

**R:**

```sql
SELECT COUNT(*) AS total_funcionarios
FROM funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
WHERE d.departamento_nome = 'Vendas';
```

**2.2** Qual é o custo total com salários do Departamento de Vendas?

**R:**

```sql
SELECT SUM(f.salario) AS custo_total_salarios
FROM funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
WHERE d.departamento_nome = 'Vendas';
```

**2.3** Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?

**R:**

```sql
SELECT ROUND(AVG(f.salario), 2) AS media_salarial
FROM funcionarios f
JOIN cargos c
    ON c.cargo_id = f.cargo_id
WHERE c.cargo_nome NOT IN ('CEO', 'CMO', 'CFO');
```

**2.4** Qual é a média salarial do Departamento de Tecnologia?

**R:**

```sql
SELECT ROUND(AVG(f.salario), 2) AS media_salarial
FROM funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
WHERE d.departamento_nome = 'Tecnologia';
```

**2.5** Qual departamento possui a maior média salarial?

**R:**

```sql
SELECT
    d.departamento_nome,
    ROUND(AVG(f.salario), 2) AS media_salarial
FROM departamentos d
JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
GROUP BY d.departamento_id, d.departamento_nome
ORDER BY media_salarial DESC
LIMIT 1;
```

**2.6** Qual departamento possui o menor número de funcionários?

**R:**

```sql
SELECT
    d.departamento_nome,
    COUNT(f.funcionario_id) AS total_funcionarios
FROM departamentos d
LEFT JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
GROUP BY d.departamento_id, d.departamento_nome
ORDER BY total_funcionarios ASC
LIMIT 1;
```

---

## Nível 3: Recursos Humanos

**3.1** Quantos funcionários da empresa Momento possuem cônjuges?

**R:**

```sql
SELECT COUNT(DISTINCT funcionario_id) AS funcionarios_com_conjuge
FROM dependentes
WHERE relacionamento = 'Cônjuge';
```

**3.2** Quantos funcionários possuem filhos registrados?

**R:**

```sql
SELECT COUNT(DISTINCT funcionario_id) AS funcionarios_com_filhos
FROM dependentes
WHERE relacionamento = 'Filha(o)';
```

**3.3** Qual funcionário foi contratado há mais tempo na empresa?

**R:**

```sql
SELECT
    primeiro_nome,
    sobrenome,
    data_contratacao
FROM funcionarios
ORDER BY data_contratacao ASC
LIMIT 1;
```

**3.4** Qual funcionário foi contratado há menos tempo na empresa?

**R:**

```sql
SELECT
    primeiro_nome,
    sobrenome,
    data_contratacao
FROM funcionarios
ORDER BY data_contratacao DESC
LIMIT 1;
```

**3.5** Liste os 5 funcionários com mais tempo de casa, ordenados pela data de contratação.

**R:**

```sql
SELECT
    primeiro_nome,
    sobrenome,
    data_contratacao
FROM funcionarios
ORDER BY data_contratacao ASC
LIMIT 5;
```

**3.6** Quantos funcionários foram contratados na década de 1990 (entre 1990-1999)?

**R:**

```sql
SELECT COUNT(*) AS total_funcionarios
FROM funcionarios
WHERE data_contratacao >= '1990-01-01'
  AND data_contratacao < '2000-01-01';
```

**3.7** Como a média salarial da Momento evoluiu ao longo dos anos? Agrupe por ano de contratação e calcule a média salarial.

**R:**

```sql
SELECT
    YEAR(data_contratacao) AS ano_contratacao,
    ROUND(AVG(salario), 2) AS media_salarial
FROM funcionarios
GROUP BY YEAR(data_contratacao)
ORDER BY ano_contratacao;
```

---

## Nível 4: Operações e Escritórios

**4.1** Qual é o custo total de suprimentos em cada escritório? Ordene do mais caro ao mais barato.

**R:**

```sql
SELECT
    e.escritorio_nome,
    SUM(s.quantidade_estoque * s.custo_unitario) AS custo_total
FROM escritorios e
JOIN suprimentos s
    ON s.escritorio_id = e.escritorio_id
GROUP BY e.escritorio_id, e.escritorio_nome
ORDER BY custo_total DESC;
```

**4.2** Qual escritório possui a maior quantidade de diferentes tipos de suprimentos?

**R:**

```sql
SELECT
    e.escritorio_nome,
    COUNT(DISTINCT s.suprimento_nome) AS tipos_suprimentos
FROM escritorios e
JOIN suprimentos s
    ON s.escritorio_id = e.escritorio_id
GROUP BY e.escritorio_id, e.escritorio_nome
ORDER BY tipos_suprimentos DESC
LIMIT 1;
```

**4.3** Qual é o suprimento mais caro (considerando preço unitário) em toda a empresa?

**R:**

```sql
SELECT
    suprimento_nome,
    custo_unitario,
    moeda
FROM suprimentos
ORDER BY custo_unitario DESC
LIMIT 1;
```

**4.4** Calcule o valor total do inventário de suprimentos da empresa (quantidade × preço unitário de todos os itens em todos os escritórios).

**R:**

```sql
SELECT
    SUM(quantidade_estoque * custo_unitario) AS valor_total_inventario
FROM suprimentos;
```

---

## Nível 5: Produtos e Vendas

**5.1** Quais produtos foram vendidos pela Momento? Liste todos os produtos únicos.

**R:**

```sql
SELECT DISTINCT
    p.produto_id,
    p.produto_nome
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
ORDER BY p.produto_nome;
```

**5.2** Qual é o produto mais vendido (maior quantidade total)?

**R:**

```sql
SELECT
    p.produto_nome,
    SUM(i.quantidade) AS quantidade_vendida
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
GROUP BY p.produto_id, p.produto_nome
ORDER BY quantidade_vendida DESC
LIMIT 1;
```

**5.3** Qual é o produto menos vendido?

**R:**

```sql
SELECT
    p.produto_nome,
    SUM(i.quantidade) AS quantidade_vendida
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
GROUP BY p.produto_id, p.produto_nome
ORDER BY quantidade_vendida ASC
LIMIT 1;
```

**5.4** Pensando na relação quantidade × valor unitário, qual produto gerou mais receita para a empresa?

**R:**

```sql
SELECT
    p.produto_nome,
    SUM(i.quantidade * i.preco_unitario) AS receita_total
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
GROUP BY p.produto_id, p.produto_nome
ORDER BY receita_total DESC
LIMIT 1;
```

**5.5** Qual é o produto mais caro (maior preço unitário) no catálogo?

**R:**

```sql
SELECT
    produto_nome,
    preco_catalogo,
    moeda
FROM produtos
ORDER BY preco_catalogo DESC
LIMIT 1;
```

**5.6** Qual foi o faturamento total da empresa em vendas?

**R:**

```sql
SELECT
    SUM(i.quantidade * i.preco_unitario) AS faturamento_total
FROM vendas v
JOIN itens_venda i
    ON i.venda_id = v.venda_id
WHERE v.status_venda_id = 1;
```

**5.7** Quantas vendas foram realizadas no mês de junho de 2023?

**R:**

```sql
SELECT COUNT(*) AS total_vendas
FROM vendas
WHERE data_venda >= '2023-06-01'
  AND data_venda < '2023-07-01';
```

**5.8** Qual vendedor realizou mais vendas (em quantidade de transações)?

**R:**

```sql
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS vendedor,
    COUNT(v.venda_id) AS total_vendas
FROM funcionarios f
JOIN vendas v
    ON v.funcionario_id = f.funcionario_id
GROUP BY f.funcionario_id, f.primeiro_nome, f.sobrenome
ORDER BY total_vendas DESC
LIMIT 1;
```

**5.9** Qual vendedor gerou mais receita para a empresa?

**R:**

```sql
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS vendedor,
    SUM(i.quantidade * i.preco_unitario) AS receita_total
FROM funcionarios f
JOIN vendas v
    ON v.funcionario_id = f.funcionario_id
JOIN itens_venda i
    ON i.venda_id = v.venda_id
WHERE v.status_venda_id = 1
GROUP BY f.funcionario_id, f.primeiro_nome, f.sobrenome
ORDER BY receita_total DESC
LIMIT 1;
```

---

## Nível 6: Operações de Atualização

**6.1** Um novo departamento foi criado: **Inovações**. Ele será alocado no escritório "Wayne Offices". Adicione-o ao banco de dados.

**R:**

```sql
INSERT INTO departamentos (
    departamento_id,
    departamento_nome,
    educacional,
    escritorio_id
)
SELECT
    31,
    'Inovações',
    0,
    escritorio_id
FROM escritorios
WHERE escritorio_nome = 'Wayne Offices';
```

**6.2** O departamento de Inovações está sem funcionários. Transfira 2 funcionários do departamento de Tecnologia para Inovações.

**R:**

```sql
UPDATE funcionarios
SET departamento_id = (
    SELECT departamento_id
    FROM departamentos
    WHERE departamento_nome = 'Inovações'
)
WHERE funcionario_id IN (
    SELECT funcionario_id
    FROM (
        SELECT f.funcionario_id
        FROM funcionarios f
        JOIN departamentos d
            ON d.departamento_id = f.departamento_id
        WHERE d.departamento_nome = 'Tecnologia'
        ORDER BY f.funcionario_id
        LIMIT 2
    ) AS selecionados
);
```

**6.3** A empresa decidiu dar um aumento de 10% para todos os funcionários do departamento de Tecnologia. Atualize os salários.

**R:**

```sql
UPDATE funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
SET f.salario = f.salario * 1.10
WHERE d.departamento_nome = 'Tecnologia';
```

**6.4** O funcionário "Bruce Ernst" foi promovido a "Senior Web Developer" e recebeu um aumento para $5.000. Atualize suas informações.

**R:**

```sql
UPDATE funcionarios
SET
    cargo_id = (
        SELECT cargo_id
        FROM cargos
        WHERE cargo_nome = 'Senior Web Developer'
    ),
    salario = 5000.00
WHERE primeiro_nome = 'Bruce'
  AND sobrenome = 'Ernst';
```

**6.5** Adicione um novo suprimento ao escritório "Wayne Offices": 15 unidades de "Headsets" com preço unitário de $150 cada.

**R:**

```sql
INSERT INTO suprimentos (
    suprimento_id,
    referencia_compra,
    suprimento_nome,
    quantidade_estoque,
    custo_unitario,
    moeda,
    data_compra,
    escritorio_id
)
SELECT
    106,
    'PO-WAYNE-HEADSET-01',
    'Headsets',
    15,
    150.00,
    'USD',
    CURDATE(),
    escritorio_id
FROM escritorios
WHERE escritorio_nome = 'Wayne Offices';
```

**6.6** Todos os funcionários contratados antes de 1990 estão aposentando. Remova-os do banco de dados (CUIDADO: execute um **SELECT** antes para ver quantos serão afetados!).

**R:**

O modelo também permite preservar o histórico dos funcionários. Por isso, é mais seguro alterar o status para `INATIVO` em vez de apagar os registros.

```sql
SELECT
    funcionario_id,
    primeiro_nome,
    sobrenome,
    data_contratacao
FROM funcionarios
WHERE data_contratacao < '1990-01-01';
```

Depois de conferir os registros:

```sql
UPDATE funcionarios
SET status_funcionario_id = (
    SELECT status_funcionario_id
    FROM status_funcionario
    WHERE nome = 'INATIVO'
)
WHERE data_contratacao < '1990-01-01';
```

---

## Nível 7: Da sala de aula ao impacto corporativo

**7.1** Quais cursos estão ativos? Para cada um, mostre carga horária, nível e quantas turmas já foram abertas.

**R:**

```sql
SELECT
    c.curso_nome,
    c.carga_horaria,
    nc.nome AS nivel,
    COUNT(t.turma_id) AS total_turmas
FROM cursos c
JOIN niveis_curso nc
    ON nc.nivel_curso_id = c.nivel_curso_id
LEFT JOIN turmas t
    ON t.curso_id = c.curso_id
WHERE c.ativo = 1
GROUP BY
    c.curso_id,
    c.curso_nome,
    c.carga_horaria,
    nc.nome;
```

**7.2** Para cada turma, mostre curso, professores, capacidade e número de matrículas.

**R:**

```sql
SELECT
    t.codigo AS turma,
    c.curso_nome,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS professor,
    t.capacidade,
    COUNT(m.matricula_id) AS total_matriculas
FROM turmas t
JOIN cursos c
    ON c.curso_id = t.curso_id
JOIN funcionarios f
    ON f.funcionario_id = t.professor_funcionario_id
LEFT JOIN matriculas m
    ON m.turma_id = t.turma_id
GROUP BY
    t.turma_id,
    t.codigo,
    c.curso_nome,
    f.primeiro_nome,
    f.sobrenome,
    t.capacidade;
```

**7.3** Quantos estudantes receberam bolsa, qual foi o percentual concedido e em quais cursos?

**R:**

```sql
SELECT
    c.curso_nome,
    COUNT(DISTINCT b.aluno_id) AS estudantes_bolsistas,
    ROUND(AVG(b.percentual_desconto), 2) AS percentual_medio,
    GROUP_CONCAT(DISTINCT a.nome) AS estudantes
FROM bolsas b
JOIN alunos a
    ON a.aluno_id = b.aluno_id
JOIN cursos c
    ON c.curso_id = b.curso_id
GROUP BY c.curso_id, c.curso_nome;
```

**7.4** Por curso e turma, calcule matrículas, concluintes, certificados emitidos e nota média. Quais perguntas adicionais seriam necessárias antes de afirmar que um curso teve “mais sucesso” que outro?

**R:**

```sql
SELECT
    c.curso_nome,
    t.codigo AS turma,
    COUNT(m.matricula_id) AS matriculas,
    SUM(sm.nome = 'CONCLUIDA') AS concluintes,
    SUM(m.certificado_emitido = 1) AS certificados_emitidos,
    ROUND(AVG(m.nota_final), 2) AS nota_media
FROM cursos c
JOIN turmas t
    ON t.curso_id = c.curso_id
LEFT JOIN matriculas m
    ON m.turma_id = t.turma_id
LEFT JOIN status_matricula sm
    ON sm.status_matricula_id = m.status_matricula_id
GROUP BY
    c.curso_nome,
    t.turma_id,
    t.codigo;
```

Antes de afirmar que um curso teve mais sucesso, seria necessário avaliar:
- A taxa de conclusão em relação ao total de matriculados.
- A quantidade de alunos que abandonaram o curso.
- A taxa de emissão de certificados.
- A média das notas dos estudantes.
- A empregabilidade dos alunos após a formação.
- A diferença de carga horária e capacidade entre os cursos.

**7.5** Reconstrua a trajetória de Ariel Nunes: curso, turma, matrícula, bolsa, estágio e contratação. Em que momento a formação passou a se conectar com uma oportunidade corporativa?

**R:**

```sql
SELECT
    a.nome AS aluno,
    c.curso_nome,
    t.codigo AS turma,
    m.data_matricula,
    sm.nome AS status_matricula,
    m.nota_final,
    b.percentual_desconto AS bolsa_percentual,
    tv.nome AS tipo_vinculo,
    sv.nome AS status_vinculo,
    vt.data_inicio,
    vt.data_fim,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario
FROM alunos a
LEFT JOIN matriculas m
    ON m.aluno_id = a.aluno_id
LEFT JOIN turmas t
    ON t.turma_id = m.turma_id
LEFT JOIN cursos c
    ON c.curso_id = t.curso_id
LEFT JOIN status_matricula sm
    ON sm.status_matricula_id = m.status_matricula_id
LEFT JOIN bolsas b
    ON b.aluno_id = a.aluno_id
    AND b.curso_id = c.curso_id
LEFT JOIN vinculos_talentos vt
    ON vt.aluno_id = a.aluno_id
LEFT JOIN tipos_vinculo tv
    ON tv.tipo_vinculo_id = vt.tipo_vinculo_id
LEFT JOIN status_vinculo sv
    ON sv.status_vinculo_id = vt.status_vinculo_id
LEFT JOIN funcionarios f
    ON f.funcionario_id = vt.funcionario_id
WHERE a.nome = 'Ariel Nunes';
```

A formação de Ariel passou a se conectar diretamente com uma oportunidade corporativa em **05/05/2025**, quando começou seu vínculo de estágio. Em **12/05/2025**, ocorreu sua contratação como funcionário da Momento.

**7.6** Liste os vínculos de talentos com estudante, tipo de vínculo, departamento, status e, quando existir, a pessoa funcionária associada. Quantas pessoas passaram por estágio e quantas chegaram à contratação?

**R:**

```sql
SELECT
    a.nome AS estudante,
    tv.nome AS tipo_vinculo,
    d.departamento_nome,
    sv.nome AS status,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario_associado
FROM vinculos_talentos vt
JOIN alunos a
    ON a.aluno_id = vt.aluno_id
JOIN tipos_vinculo tv
    ON tv.tipo_vinculo_id = vt.tipo_vinculo_id
JOIN departamentos d
    ON d.departamento_id = vt.departamento_id
JOIN status_vinculo sv
    ON sv.status_vinculo_id = vt.status_vinculo_id
LEFT JOIN funcionarios f
    ON f.funcionario_id = vt.funcionario_id;
```

Para contar as pessoas:

```sql
SELECT
    tv.nome AS tipo_vinculo,
    COUNT(DISTINCT vt.aluno_id) AS total_pessoas
FROM vinculos_talentos vt
JOIN tipos_vinculo tv
    ON tv.tipo_vinculo_id = vt.tipo_vinculo_id
GROUP BY tv.nome;
```

**7.7** Quais departamentos recebem talentos da escola?

**R:**

```sql
SELECT DISTINCT
    d.departamento_nome
FROM vinculos_talentos vt
JOIN departamentos d
    ON d.departamento_id = vt.departamento_id
ORDER BY d.departamento_nome;
```

**7.8** Crie uma única consulta que apresente: total de alunos, matrículas ativas, concluintes, bolsas concedidas, estágios, contratações e departamentos que receberam talentos.

**R:**

```sql
SELECT
    (SELECT COUNT(*)
     FROM alunos) AS total_alunos,

    (SELECT COUNT(*)
     FROM matriculas m
     JOIN status_matricula sm
        ON sm.status_matricula_id = m.status_matricula_id
     WHERE sm.nome = 'ATIVA') AS matriculas_ativas,

    (SELECT COUNT(*)
     FROM matriculas m
     JOIN status_matricula sm
        ON sm.status_matricula_id = m.status_matricula_id
     WHERE sm.nome = 'CONCLUIDA') AS concluintes,

    (SELECT COUNT(*)
     FROM bolsas) AS bolsas_concedidas,

    (SELECT COUNT(DISTINCT vt.aluno_id)
     FROM vinculos_talentos vt
     JOIN tipos_vinculo tv
        ON tv.tipo_vinculo_id = vt.tipo_vinculo_id
     WHERE tv.nome = 'ESTAGIO') AS estagios,

    (SELECT COUNT(DISTINCT vt.aluno_id)
     FROM vinculos_talentos vt
     JOIN tipos_vinculo tv
        ON tv.tipo_vinculo_id = vt.tipo_vinculo_id
     WHERE tv.nome = 'CONTRATACAO') AS contratacoes,

    (SELECT COUNT(DISTINCT departamento_id)
     FROM vinculos_talentos) AS departamentos_com_talentos;
```

**7.11** O que faz a tabela **vinculos_talentos**?

**R:**

A tabela `vinculos_talentos` registra a relação entre os estudantes da escola e as oportunidades profissionais oferecidas pela Momento.

Ela permite acompanhar:
- Qual estudante participou da oportunidade.
- Qual funcionário foi associado à contratação, quando existir.
- Em qual departamento ocorreu o vínculo.
- Qual foi o tipo de vínculo, como estágio ou contratação.
- A data de início e término.
- O status atual do vínculo.

Assim, a empresa consegue acompanhar a trajetória dos alunos desde a formação até sua entrada no mercado de trabalho.

---

## Nível 8: Duas Frentes, Duas Operações

**8.1** Quanto custa manter a frente educacional em comparação à operação comercial? (Faça uma análise de custo total com salários, suprimentos e despesas gerais para cada departamento.)

**R:**

```sql
SELECT
    d.departamento_nome,
    d.educacional,
    SUM(f.salario) AS total_salarios,
    COALESCE(SUM(s.quantidade_estoque * s.custo_unitario), 0)
        AS total_suprimentos,
    SUM(f.salario) +
    COALESCE(SUM(s.quantidade_estoque * s.custo_unitario), 0)
        AS custo_total
FROM departamentos d
LEFT JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
LEFT JOIN escritorios e
    ON e.escritorio_id = d.escritorio_id
LEFT JOIN suprimentos s
    ON s.escritorio_id = e.escritorio_id
GROUP BY
    d.departamento_id,
    d.departamento_nome,
    d.educacional;
```

**Observação:** o banco apresenta salários e suprimentos, mas não possui uma tabela específica de despesas gerais. Além disso, os suprimentos são registrados por escritório, não por departamento. Para evitar duplicidade, o ideal é separar esses custos em consultas diferentes e depois consolidá-los.

**8.2** Quais escritórios concentram equipes comerciais e quais concentram equipes da escola?

**R:**

```sql
SELECT
    e.escritorio_nome,
    d.educacional,
    COUNT(f.funcionario_id) AS total_funcionarios
FROM escritorios e
JOIN departamentos d
    ON d.escritorio_id = e.escritorio_id
LEFT JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
GROUP BY
    e.escritorio_id,
    e.escritorio_nome,
    d.educacional
ORDER BY e.escritorio_nome;
```

**8.3** A receita das vendas consegue sustentar investimentos educacionais?

**R:**

```sql
SELECT
    SUM(i.quantidade * i.preco_unitario) AS receita_vendas
FROM vendas v
JOIN itens_venda i
    ON i.venda_id = v.venda_id
WHERE v.status_venda_id = 1;
```

Para comparar com o custo educacional, é necessário somar os salários dos departamentos educacionais e os custos de suprimentos atribuídos a essa frente.

A receita sozinha não permite afirmar que os investimentos são sustentáveis sem considerar os demais custos e as moedas utilizadas.

**8.4** Quais cargos e departamentos são compartilhados pelas duas frentes?

**R:**

```sql
SELECT
    c.cargo_nome,
    COUNT(DISTINCT CASE
        WHEN d.educacional = 0 THEN d.departamento_id
    END) AS departamentos_comerciais,
    COUNT(DISTINCT CASE
        WHEN d.educacional = 1 THEN d.departamento_id
    END) AS departamentos_educacionais
FROM funcionarios f
JOIN cargos c
    ON c.cargo_id = f.cargo_id
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
GROUP BY c.cargo_id, c.cargo_nome
HAVING departamentos_comerciais > 0
   AND departamentos_educacionais > 0;
```

**8.5** Como a expansão internacional afeta vendas, equipes e o projeto educacional?

**R:**

A expansão internacional pode ser analisada comparando os países, escritórios, funcionários e vendas.

```sql
SELECT
    p.pais_nome,
    COUNT(DISTINCT e.escritorio_id) AS total_escritorios,
    COUNT(DISTINCT f.funcionario_id) AS total_funcionarios,
    COUNT(DISTINCT v.venda_id) AS total_vendas,
    SUM(i.quantidade * i.preco_unitario) AS receita_total
FROM paises p
JOIN escritorios e
    ON e.pais_id = p.pais_id
LEFT JOIN departamentos d
    ON d.escritorio_id = e.escritorio_id
LEFT JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
LEFT JOIN vendas v
    ON v.funcionario_id = f.funcionario_id
LEFT JOIN itens_venda i
    ON i.venda_id = v.venda_id
GROUP BY p.pais_id, p.pais_nome;
```

A análise educacional também pode ser feita por escritório e país, considerando as turmas e os professores. É importante observar que os valores estão registrados em moedas diferentes, então uma comparação financeira exige conversão cambial.

---

## Nível 9: Análise Avançada com Agregações

**9.1** Crie um relatório mostrando cada departamento com: nome do departamento, número de funcionários, salário total e média salarial.

**R:**

```sql
SELECT
    d.departamento_nome,
    COUNT(f.funcionario_id) AS total_funcionarios,
    COALESCE(SUM(f.salario), 0) AS salario_total,
    ROUND(AVG(f.salario), 2) AS media_salarial
FROM departamentos d
LEFT JOIN funcionarios f
    ON f.departamento_id = d.departamento_id
GROUP BY d.departamento_id, d.departamento_nome;
```

**9.2** Liste os 3 cargos mais comuns na empresa e quantos funcionários ocupam cada cargo.

**R:**

```sql
SELECT
    c.cargo_nome,
    COUNT(f.funcionario_id) AS total_funcionarios
FROM cargos c
JOIN funcionarios f
    ON f.cargo_id = c.cargo_id
GROUP BY c.cargo_id, c.cargo_nome
ORDER BY total_funcionarios DESC
LIMIT 3;
```

**9.3** Encontre todos os funcionários que ganham acima da média salarial de seus respectivos departamentos.

**R:**

```sql
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario,
    d.departamento_nome,
    f.salario
FROM funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
WHERE f.salario > (
    SELECT AVG(f2.salario)
    FROM funcionarios f2
    WHERE f2.departamento_id = f.departamento_id
);
```

**9.4** Calcule a taxa de crescimento da empresa por ano (quantos funcionários foram contratados a cada ano).

**R:**

```sql
SELECT
    YEAR(data_contratacao) AS ano,
    COUNT(*) AS contratacoes
FROM funcionarios
GROUP BY YEAR(data_contratacao)
ORDER BY ano;
```

**9.5** Crie um ranking dos vendedores, mostrando: nome do vendedor, número de vendas realizadas e receita total gerada.

**R:**

```sql
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS vendedor,
    COUNT(DISTINCT v.venda_id) AS total_vendas,
    COALESCE(SUM(i.quantidade * i.preco_unitario), 0) AS receita_total
FROM funcionarios f
LEFT JOIN vendas v
    ON v.funcionario_id = f.funcionario_id
LEFT JOIN itens_venda i
    ON i.venda_id = v.venda_id
GROUP BY
    f.funcionario_id,
    f.primeiro_nome,
    f.sobrenome
ORDER BY receita_total DESC;
```

**9.6** Encontre os produtos que foram vendidos por apenas um vendedor.

**R:**

```sql
SELECT
    p.produto_nome,
    COUNT(DISTINCT v.funcionario_id) AS total_vendedores
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
JOIN vendas v
    ON v.venda_id = i.venda_id
GROUP BY p.produto_id, p.produto_nome
HAVING COUNT(DISTINCT v.funcionario_id) = 1;
```

---

## Nível 10: Auditoria de Vendas

**10.1** Encontre vendas que não possuem vendedor associado.

**R:**

```sql
SELECT *
FROM vendas
WHERE funcionario_id IS NULL;
```

No modelo apresentado, `funcionario_id` é obrigatório (`NOT NULL`) e possui chave estrangeira para `funcionarios`. Portanto, o banco impede a inserção de vendas sem um vendedor associado.

**10.2** Verifique se há vendedores registrados nas vendas que não existem na coleção de funcionários.

**R:**

```sql
SELECT v.*
FROM vendas v
LEFT JOIN funcionarios f
    ON f.funcionario_id = v.funcionario_id
WHERE f.funcionario_id IS NULL;
```

A chave estrangeira da tabela `vendas` impede que seja associado um funcionário inexistente, desde que as restrições estejam habilitadas.

**10.3** Identifique produtos que aparecem nas vendas mas com preços unitários muito diferentes entre transações (variação > 10%).

**R:**

```sql
SELECT
    p.produto_nome,
    MIN(i.preco_unitario) AS menor_preco,
    MAX(i.preco_unitario) AS maior_preco,
    ROUND(
        ((MAX(i.preco_unitario) - MIN(i.preco_unitario))
        / NULLIF(MIN(i.preco_unitario), 0)) * 100,
        2
    ) AS variacao_percentual
FROM produtos p
JOIN itens_venda i
    ON i.produto_id = p.produto_id
GROUP BY p.produto_id, p.produto_nome
HAVING variacao_percentual > 10;
```

**10.4** O CEO pediu um dashboard completo.

Crie UMA ÚNICA query de agregação que retorne:

- Total de funcionários
- Custo total com salários
- Número de departamentos
- Número de escritórios
- Receita total de vendas
- Produto mais vendido

**R:**

```sql
SELECT
    (SELECT COUNT(*)
     FROM funcionarios) AS total_funcionarios,

    (SELECT SUM(salario)
     FROM funcionarios) AS custo_total_salarios,

    (SELECT COUNT(*)
     FROM departamentos) AS total_departamentos,

    (SELECT COUNT(*)
     FROM escritorios) AS total_escritorios,

    (SELECT SUM(i.quantidade * i.preco_unitario)
     FROM vendas v
     JOIN itens_venda i
        ON i.venda_id = v.venda_id
     WHERE v.status_venda_id = 1) AS receita_total,

    (SELECT p.produto_nome
     FROM produtos p
     JOIN itens_venda i
        ON i.produto_id = p.produto_id
     GROUP BY p.produto_id, p.produto_nome
     ORDER BY SUM(i.quantidade) DESC
     LIMIT 1) AS produto_mais_vendido;
```

---

## Nível 11: ETL do CSV

O arquivo `vendas_campanha_bruto.csv` contém dados de pedidos e itens em formatos variados.

**R:** O processo de ETL deve ser dividido em três etapas:

**1. Extract — Extração**

Importar os dados do CSV para a tabela `stg_vendas_csv`, mantendo os valores originais.

```sql
SELECT *
FROM stg_vendas_csv;
```

**2. Transform — Transformação**

Validar os dados, corrigir espaços, padronizar textos e converter datas, quantidades e preços.

Exemplo de identificação de valores vazios:

```sql
SELECT *
FROM stg_vendas_csv
WHERE sku_raw IS NULL
   OR quantidade_raw IS NULL
   OR preco_unitario_raw IS NULL
   OR data_venda_raw IS NULL;
```

**3. Load — Carregamento**

Após a validação, inserir os registros aceitos nas tabelas `vendas` e `itens_venda`.

Os registros que apresentarem problemas devem ser documentados em `etl_rejeicoes`.

Exemplo:

```sql
INSERT INTO etl_rejeicoes (
    rejeicao_id,
    source_row_id,
    campo,
    valor_original,
    motivo,
    acao_etl_id
)
VALUES (
    1,
    1,
    'sku_raw',
    'SKU_INVALIDO',
    'SKU não encontrado no cadastro de produtos',
    2
);
```

A tabela de rejeições permite manter o histórico dos problemas encontrados durante a importação.

**Observação:** o CSV não foi disponibilizado junto ao material analisado. Portanto, não é possível informar quantas linhas serão aceitas ou rejeitadas sem examinar esse arquivo.

---

## Nível 12: Alterações seguras

**R:** As alterações devem ser executadas dentro de transações, permitindo conferir os resultados antes de confirmar as mudanças.

Exemplo:

```sql
START TRANSACTION;

-- Realizar as alterações necessárias.

-- Conferir os resultados.
SELECT *
FROM funcionarios;

-- Se estiver correto:
-- COMMIT;

-- Se precisar desfazer:
ROLLBACK;
```

**Criar um departamento novo em um escritório existente:**

```sql
INSERT INTO departamentos (
    departamento_id,
    departamento_nome,
    educacional,
    escritorio_id
)
SELECT
    31,
    'Inovações',
    0,
    escritorio_id
FROM escritorios
WHERE escritorio_nome = 'Wayne Offices';
```

**Cadastrar o cargo Senior Web Developer:**

```sql
INSERT INTO cargos (
    cargo_id,
    cargo_nome,
    min_salario,
    max_salario
)
VALUES (
    41,
    'Senior Web Developer',
    5000.00,
    15000.00
);
```

**Promover Bruce Ernst:**

```sql
UPDATE funcionarios
SET
    cargo_id = 41,
    salario = 5000.00
WHERE primeiro_nome = 'Bruce'
  AND sobrenome = 'Ernst';
```

**Transferir duas pessoas de Tecnologia para Inovações:**

```sql
UPDATE funcionarios
SET departamento_id = 31
WHERE funcionario_id IN (
    SELECT funcionario_id
    FROM (
        SELECT f.funcionario_id
        FROM funcionarios f
        JOIN departamentos d
            ON d.departamento_id = f.departamento_id
        WHERE d.departamento_nome = 'Tecnologia'
        ORDER BY f.funcionario_id
        LIMIT 2
    ) AS selecionados
);
```

**Reajustar salários de quem permanecer em Tecnologia:**

```sql
UPDATE funcionarios f
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
SET f.salario = f.salario * 1.10
WHERE d.departamento_nome = 'Tecnologia';
```

**Adicionar a compra de headsets:**

```sql
INSERT INTO suprimentos (
    suprimento_id,
    referencia_compra,
    suprimento_nome,
    quantidade_estoque,
    custo_unitario,
    moeda,
    data_compra,
    escritorio_id
)
VALUES (
    106,
    'PO-WAYNE-HEADSET-01',
    'Headsets',
    15,
    150.00,
    'USD',
    CURDATE(),
    3000
);
```

**Simular aposentadoria sem apagar o histórico:**

```sql
UPDATE funcionarios
SET status_funcionario_id = 3
WHERE data_contratacao < '1990-01-01';
```

**Conferir as alterações:**

```sql
SELECT *
FROM funcionarios
ORDER BY funcionario_id;

SELECT *
FROM departamentos
ORDER BY departamento_id;

SELECT *
FROM suprimentos
ORDER BY suprimento_id;
```

Utilize `COMMIT` somente quando todos os resultados estiverem corretos. Caso contrário, utilize `ROLLBACK`.

---

## Consultas complementares — Views

As views permitem salvar consultas e reutilizar relatórios sem precisar escrever novamente todos os relacionamentos.

**Exemplo: dados dos funcionários**

```sql
CREATE OR REPLACE VIEW dados_funcionarios AS
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario_nome,
    f.telefone,
    f.email,
    f.data_contratacao,
    sf.nome AS situacao,
    f.salario,
    f.moeda_salario,
    c.cargo_nome AS cargo,
    d.departamento_nome AS departamento,
    e.escritorio_nome AS escritorio,
    p.pais_nome AS pais
FROM funcionarios f
JOIN cargos c
    ON c.cargo_id = f.cargo_id
JOIN departamentos d
    ON d.departamento_id = f.departamento_id
JOIN escritorios e
    ON e.escritorio_id = d.escritorio_id
JOIN paises p
    ON p.pais_id = e.pais_id
JOIN status_funcionario sf
    ON sf.status_funcionario_id = f.status_funcionario_id;
```

**Exemplo: desempenho dos funcionários em vendas**

```sql
CREATE OR REPLACE VIEW vendas_funcionario_performance AS
SELECT
    f.funcionario_id,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario_nome,
    COUNT(DISTINCT v.venda_id) AS total_vendas,
    COALESCE(SUM(i.quantidade), 0) AS unidades_vendidas,
    COALESCE(
        SUM(i.quantidade * i.preco_unitario),
        0
    ) AS receita_total
FROM funcionarios f
LEFT JOIN vendas v
    ON v.funcionario_id = f.funcionario_id
LEFT JOIN itens_venda i
    ON i.venda_id = v.venda_id
GROUP BY
    f.funcionario_id,
    f.primeiro_nome,
    f.sobrenome;
```

**Exemplo: relatório de vendas**

```sql
CREATE OR REPLACE VIEW relatorio_vendas AS
SELECT
    v.venda_id AS ticket,
    v.referencia_origem,
    v.data_venda,
    cv.nome AS canal,
    sv.nome AS status_venda,
    i.numero_item,
    i.quantidade,
    i.preco_unitario,
    i.moeda,
    i.quantidade * i.preco_unitario AS receita_item,
    p.produto_nome AS produto,
    CONCAT(f.primeiro_nome, ' ', f.sobrenome) AS funcionario_nome
FROM vendas v
JOIN itens_venda i
    ON i.venda_id = v.venda_id
JOIN produtos p
    ON p.produto_id = i.produto_id
JOIN funcionarios f
    ON f.funcionario_id = v.funcionario_id
JOIN canais_venda cv
    ON cv.canal_venda_id = v.canal_venda_id
JOIN status_venda sv
    ON sv.status_venda_id = v.status_venda_id;
```

---

## Consultas complementares — Subconsulta

**Selecionando dados através de uma subconsulta**

**R:**

```sql
SELECT
    funcionarios.primeiro_nome,
    funcionarios.sobrenome
FROM funcionarios
WHERE cargo_id = (
    SELECT cargo_id
    FROM cargos
    WHERE cargo_nome = 'Contador'
);
```

A subconsulta localiza o `cargo_id` correspondente ao cargo de Contador. A consulta principal utiliza esse resultado para retornar os funcionários que possuem esse cargo.
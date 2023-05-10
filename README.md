# PostgreSQL Sizes

TAMANHO DOS BANCOS DE DADOS
```
(SELECT
	datname                                   AS banco,
	pg_database_size(datname)                 AS tamanho,
	pg_size_pretty(pg_database_size(datname)) AS tamanho_pretty
FROM pg_database
WHERE datname NOT IN ('template0', 'template1', 'postgres')
ORDER BY tamanho DESC, banco ASC)
UNION ALL
(SELECT
	'TOTAL'                                        AS banco,
	sum(pg_database_size(datname))                 AS tamanho,
	pg_size_pretty(sum(pg_database_size(datname))) AS tamanho_pretty
FROM pg_database
WHERE datname NOT IN ('template0', 'template1', 'postgres'));
```

TAMANHO DAS TABELAS
```
SELECT 
	esquema, 
	tabela,
	pg_size_pretty(pg_relation_size(esq_tab)) AS tamanho,
	pg_size_pretty(pg_total_relation_size(esq_tab)) AS tamanho_total_com_indices,
	pg_total_relation_size(esq_tab) AS tamanho_total_nao_formatado,
	trunc((pg_total_relation_size(esq_tab)/pg_database_size(current_database())::NUMERIC*100),2) AS percentual
FROM (
	SELECT tablename AS tabela,
    	schemaname AS esquema,
		schemaname||'.'||tablename AS esq_tab
	FROM pg_catalog.pg_tables
	WHERE schemaname NOT IN ('pg_catalog', 'information_schema', 'pg_toast')
	) t
ORDER BY pg_total_relation_size(esq_tab) DESC;
```

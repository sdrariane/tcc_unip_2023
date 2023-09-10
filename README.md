## Comandos SQL Server
### Inserção em massa dos dados
<p> Como são ***mais de 5 milhões de dados***, é completamente inviável fazer isso na mão um por um, assim como por limitações técnicas e financeiras, é impossível realizar através da versão gratuita do Microsoft Power Automate em uma máquina com apenas 8GB de RAM. Então, pelo método raiz, realizei o seguinte comando com algumas observações: </p>

```sql
BULK INSERT tabela FROM 'caminho_do_arquivo' WITH (FORMAT = 'CSV', CODEPAGE = '65001', FIRSTROW = 2, FIELDTERMINATOR = ';', ROWTERMINATOR = '\n');
```
   
- o arquivo deve estar salvo no formato CSV dentro do padrão UTF8 (alfabeto português brasileiro);
- o **CODEPAGE** faz referência à formatação do arquio CSV que está em UTF8;
- o **FIRSTROW** indica onde os valores devem começar a ser cntabilizados ignorando um certo numero delinhas acima que representam o cabeçalho e se iniciam em 1;
- o **FIEDLTERMINATOR** diz respeito a como o **ÚLTIMO CAMPO** que contém informações do arquivo CSV se encerra;
- o **ROWTERMINATOR** diz respeito a como a **ÚLTIMA LINHA** que contém informações do arquivo CSV se encerra e para cada qual existe um código distinto. Para saber como seu arquivo está terminado, basta abrir no WordPad ou no Editor de Texto e ir até o final.

### Média das notas SiSU
<p> No `.csv` em que fora realizado o BULK das informações, as notas estavam no formato numérico com vírgula (','), e para o SQL Server contabilizar como número e permitir a execução de funções e operações numéricas, ele precisa estar no formato com ponto ('.'). Então, primeiro converti todas as colunas de todas as tabelas de varchar para float e depois, executei a média de todas as notas, de todas as colunas, de todas as tabelas. </p>

```sql
-- MÉDIAS DAS NOTAS POR ÁREA
DECLARE @valor_L2020 FLOAT SELECT @valor_L2020 = AVG(CAST(nota_l AS FLOAT)) FROM [tabela]; -- Linguagens
   
-- NÚMERO DE INSCRITOS
DECLARE @inscritos_2020 INT SELECT @inscritos_2020 = COUNT(*) FROM [tabela];
    
-- NÚMERO DE 'M' & 'F'
DECLARE @fies_M2020 INT SELECT @fies_Am2020 = SUM(CASE WHEN sexo LIKE '%M%' THEN 1 ELSE 0 END) FROM [tabela];
    
-- NÚMERO DE BRANCOS, AMARELOS E PPI
DECLARE @fies_Am2020 INT SELECT @fies_Am2020 = SUM(CASE WHEN raca_cor LIKE '%AMARELA%' THEN 1 ELSE 0 END) FROM [tabela];
    
-- RENDA PER CAPITA POR CLASSE
DECLARE @a FLOAT = 0;
DECLARE @b FLOAT = 1254;
DECLARE @c FLOAT = 1255;
DECLARE @d FLOAT = 2004;
DECLARE @e FLOAT = 2005;
DECLARE @f FLOAT = 8640;
DECLARE @g FLOAT = 8641;
DECLARE @h FLOAT = 11261;
DECLARE @i FLOAT = 11262;
    
DECLARE @classe_E INT SELECT @classe_E = COUNT(*) FROM [tabela] WHERE renda_familiar >= @a AND renda_familiar <= @b;
...
DECLARE @classe_A INT SELECT @classe_A = COUNT(*) FROM [tabela] WHERE renda_familiar >= @i;
    
-- MEMBROS FAMÍLIA
DECLARE @j FLOAT = 1;
DECLARE @k FLOAT = 2;
DECLARE @l FLOAT = 3;
DECLARE @m FLOAT = 4;
    
DECLARE @n_1 INT SELECT @n_1 = COUNT(*) FROM [tabela] WHERE n_familia = @j;
...
DECLARE @n_a INT SELECT @n_a = COUNT(*) FROM [tabela] WHERE n_familia >= @m;
```
	
### De varchar para float
<p> Tive que tratar de nulos, mudança de vírgula para ponto e etc.</p>

```sql
-- VÍRGULA PARA PONTO
UPDATE [tabela] SET [coluna] = REPLACE([coluna], ',', '.') WHERE ISNUMERIC([coluna]) = 1;
    
-- TRATA ESPAÇOS EM BRANCO COMO NULOS
UPDATE [tabela] SET [coluna] = NULL WHERE [coluna] = '';
    
-- REMOVE ESPAÇOS EM BRANCO
UPDATE [tabela] SET [coluna] = LTRIM(RTRIM([coluna]));
    
-- CONVERTE PARA FLOAT/INT
UPDATE [tabela] SET [coluna] = CAST([coluna] AS FLOAT);
```

### Dados Disque 100

#### Agrupamento por Região
```sql
DECLARE @sul INT SELECT @sul = SUM(CASE WHEN D.vitima_uf IN ('PR', 'RS', 'SC') AND CHARINDEX('Crianças e adolescentes', D.grupo_violacao) > 0 THEN 1 ELSE 0 END)
FROM [tabela] AS D;
DECLARE @norte INT SELECT @norte = SUM(CASE WHEN D.vitima_uf IN ('AC', 'AP', 'AM', 'PA', 'RO', 'RR', 'TO') AND CHARINDEX('Crianças e adolescentes', D.grupo_violacao) > 0 THEN 1 ELSE 0 END)
FROM [tabela] AS D;
DECLARE @sudeste INT SELECT @sudeste = SUM(CASE WHEN D.vitima_uf IN ('SP', 'RJ', 'ES', 'MG') AND CHARINDEX('Crianças e adolescentes', D.grupo_violacao) > 0 THEN 1 ELSE 0 END)
FROM [tabela] AS D;
DECLARE @nordeste INT SELECT @nordeste = SUM(CASE WHEN D.vitima_uf IN ('AL', 'BA', 'CE', 'MA', 'PB', 'PE', 'PI', 'RN', 'SE') AND CHARINDEX('Crianças e adolescentes', D.grupo_violacao) > 0 THEN 1 ELSE 0 END)
FROM [tabela] AS D;
DECLARE @centro_oeste INT SELECT @centro_oeste = SUM(CASE WHEN D.vitima_uf IN ('DF', 'GO', 'MT', 'MS') AND CHARINDEX('Crianças e adolescentes', D.grupo_violacao) > 0 THEN 1 ELSE 0 END)
FROM [tabela] AS D;
```

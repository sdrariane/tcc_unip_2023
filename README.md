## Comandos SQL Server
### Inserção em massa dos dados
<p> Como são ***mais de 5 milhões de dados***, é completamente inviável fazer isso na mão um por um, assim como por limitações técnicas e financeiras, é impossível realizar através da versão gratuita do Microsoft Power Automate em uma máquina com apenas 8GB de RAM. Então, pelo método raiz, realizei o seguinte comando com algumas observações: </p>

    BULK INSERT tabela FROM 'caminho_do_arquivo'
    WITH (FORMAT = 'CSV', CODEPAGE = '65001', FIRSTROW = 2, FIELDTERMINATOR = ';', ROWTERMINATOR = '\n');
   
- o arquivo deve estar salvo no formato CSV dentro do padrão UTF8 (alfabeto português brasileiro);
- o **CODEPAGE** faz referência à formatação do arquio CSV que está em UTF8;
- o **FIRSTROW** indica onde os valores devem começar a ser cntabilizados ignorando um certo numero delinhas acima que representam o cabeçalho e se iniciam em 1;
- o **FIEDLTERMINATOR** diz respeito a como o **ÚLTIMO CAMPO** que contém informações do arquivo CSV se encerra;
- o **ROWTERMINATOR** diz respeito a como a **ÚLTIMA LINHA** que contém informações do arquivo CSV se encerra e para cada qual existe um código distinto. Para saber como seu arquivo está terminado, basta abrir no WordPad ou no Editor de Texto e ir até o final.

### Média das notas SiSU
<p> No `.csv` em que fora realizado o BULK das informações, as notas estavam no formato numérico com vírgula (','), e para o SQL Server contabilizar como número e permitir a execução de funções e operações numéricas, ele precisa estar no formato com ponto ('.'). Então, primeiro converti todas as colunas de todas as tabelas de varchar para float e depois, executei a média de todas as notas, de todas as colunas, de todas as tabelas. </p>

    UPDATE tabela
    SET coluna = REPLACE(coluna, ',', '.') -- substitui vírgulas por pontos
    WHERE ISNUMERIC(coluna) = 1; -- filtra apenas valores numéricos
    
    UPDATE tabela
    SET coluna = NULL
    WHERE coluna = ''; -- trata espaços em branco como valores nulos
    
    UPDATE tabela
    SET coluna = LTRIM(RTRIM(coluna)); -- remove espaços em branco
    
    SELECT AVG(CAST(coluna AS FLOAT)) AS 'sisu[DISCIPLINA]Media2017' FROM tabela; -- faz a média da todos os valores convertendo-os em float e os apresentando em varchar dado que a configuração da coluna apresenta os dados em varchar
    DECLARE @valor_[DISCIPLINA]2017 VARCHAR(50)
    SET @valor_[DISCIPLINA]2017 ='sisu[DISCIPLINA]Media2017' -- armazena o resultado em uma variável varchar
    PRINT @valor_[DISCIPLINA]2017
	
### Inscrições SiSU
<p> A contabilizaçao das inscrições para posterior comparação grafica dos dados se deu por meio do seguinte comando: </p>

    SELECT COUNT(*) AS 'variavel_varchar' FROM tabela;

<p> Posteriormente após inseridas em uma tabela única de dados foram convertidas para `float` como explicitado anteriormente. </p>

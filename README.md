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

    -- NOTAS SEM PESO
    DECLARE @valor_[inicial da área][ano] FLOAT SELECT @valor_[inicial da área][ano] = AVG(CAST(nome_da_coluna AS FLOAT)) FROM nome_da_tabela;
    
    -- NÚMERO DE INSCRITOS
    DECLARE @inscritos_[ano] INT SELECT @inscritos_ano] = COUNT(*) FROM nome_da_tabela;
    
    -- INSERÇÃO NA TABELA
    INSERT INTO nova_tabela (ano, inscritos, área)
    VALUES (2017, @inscritos_[ano], @valor_[inicial da área][ano]);
	
### Inscrições SiSU
<p> A contabilizaçao das inscrições para posterior comparação grafica dos dados se deu por meio do seguinte comando: </p>

    SELECT COUNT(*) AS 'variavel_varchar' FROM tabela;

<p> Posteriormente após inseridas em uma tabela única de dados foram convertidas para `float` como explicitado anteriormente. </p>

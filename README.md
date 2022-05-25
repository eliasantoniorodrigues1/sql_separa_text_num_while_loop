# SQL - Query para separar Texto de Numero - Versoes antigas do SQL Server

        DECLARE @DATA AS DATETIME = '20220519'
        DECLARE @FIM AS INT = (SELECT COUNT(*) FROM SUA_TABELA WITH (NOLOCK) WHERE SEU_CRITERIO = @DATA)
        DECLARE @INI AS INT = 1

        -- CRIA A TABELA DE RESTEL
        SELECT 
            , CAMPO_TEXTO_NUMERO
            , RANK() OVER (ORDER BY RESNUM) AS [RANK]
        INTO #TEMPDB
        FROM
            TABELA WITH (NOLOCK)
        WHERE
            SEU_CRITERIO = @DATA
        ----------------------------------------
        --- SELECT
        WHILE @INI <= @FIM
          BEGIN
            DECLARE @STRING AS VARCHAR(60) = (SELECT RESTEL FROM #TEMPDB WHERE RANK = @INI)
            DECLARE @POS AS INT = PATINDEX('%[^0-9]%', @STRING)

            PRINT(@STRING)
            WHILE @POS > 0
              BEGIN
                SET @STRING = STUFF(@STRING, @POS, 1, '')
                SET @POS = PATINDEX('%[^0-9]%', @STRING)
              END
            UPDATE #RESTEL 
            SET CAMPO_TEXTO_NUMERO = @STRING 
            WHERE @INI = RANK
            SET @INI = @INI + 1
          END
        -----------------------------------------------------------------
        SELECT 
            * 
        FROM 
            #TEMPDB

        DROP TABLE #TEMPDB



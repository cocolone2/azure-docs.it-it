---
title: Usare Apache Beeline con Apache Hive - Azure HDInsight
description: Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 77a451cb9f6598bbe7013f4215cfa7cab40186bd
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037534"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usare il client Apache Beeline con Apache Hive

Informazioni su come usare [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Apache Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Per eseguire l'installazione di Oneline in locale, vedere [installare un client](#install-beeline-client)con estensione. Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. Gli esempi seguenti forniscono le stringhe di connessione più comuni usate per la connessione a HDInsight da deeline.

## <a name="types-of-connections"></a>Tipi di connessioni

### <a name="from-an-ssh-session"></a>Da una sessione SSH

Quando ci si connette da una sessione SSH a un cluster nodo Head, è possibile connettersi all'indirizzo `headnodehost` sulla porta `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Su una rete virtuale di Azure

Quando ci si connette da un client a HDInsight tramite una rete virtuale di Azure, è necessario fornire il nome di dominio completo (FQDN) di un nodo head del cluster. Poiché questa connessione viene eseguita direttamente ai nodi del cluster, la connessione usa la porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Sostituire `<headnode-FQDN>` con il nome di dominio completo di un cluster nodo head. Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Per HDInsight Enterprise Security Package cluster (ESP) con Kerberos

Quando ci si connette da un client a un cluster Enterprise Security Package (ESP) aggiunto ad Azure Active Directory (AAD)-DS in un computer nella stessa area di autenticazione del cluster, è necessario specificare anche il nome di dominio `<AAD-Domain>` e il nome di un account utente di dominio con le autorizzazioni per accedere al cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Sostituire `<username>` con il nome di un account nel dominio che disponga delle autorizzazioni per accedere al cluster. Sostituire `<AAD-DOMAIN>` con il nome del Azure Active Directory (AAD) a cui viene aggiunto il cluster. Usare una stringa maiuscola per il valore `<AAD-DOMAIN>`; in caso contrario, le credenziali non verranno trovate. Se necessario, controllare `/etc/krb5.conf` per i nomi dell'area di autenticazione.

Per trovare l'URL JDBC da Ambari:

1. Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, dove `CLUSTERNAME` è il nome del cluster. Verificare che HiveServer2 sia in esecuzione.

1. Usare gli Appunti per copiare l'URL JDBC HiveServer2.

---

### <a name="over-public-or-private-endpoints"></a>Su endpoint pubblici o privati

Quando ci si connette a un cluster usando gli endpoint pubblici o privati, è necessario specificare il nome dell'account di accesso del cluster (impostazione predefinita `admin`) e la password. Ad esempio, l'uso di Beeline da un sistema client per connettersi all'indirizzo `clustername.azurehdinsight.net`. Questa connessione viene eseguita sulla porta `443`e viene crittografata tramite SSL.

Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, usare l'UPN completo, ad esempio user@domain.com. Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

o per l'endpoint privato:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dal reti virtuali con peering nella stessa area. Per altre informazioni, vedere [vincoli sul peering VNet globale e sui bilanciamenti del carico](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . È possibile usare il comando `curl` con `-v` opzione per risolvere eventuali problemi di connettività con gli endpoint pubblici o privati prima di usare l'oggetto.

---

### <a name="use-beeline-with-apache-spark"></a>Usare l'utilizzo di con Apache Spark

Apache Spark fornisce la propria implementazione di HiveServer2, spesso definita come server Spark Thrift. Questo servizio usa Spark SQL invece di Hive per risolvere le query e può offrire prestazioni migliori a seconda della query.

#### <a name="through-public-or-private-endpoints"></a>Tramite endpoint pubblici o privati

La stringa di connessione utilizzata è leggermente diversa. Anziché contenere `httpPath=/hive2` `httpPath/sparkhive2`. Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, usare l'UPN completo, ad esempio user@domain.com. Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

o per l'endpoint privato:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dal reti virtuali con peering nella stessa area. Per altre informazioni, vedere [vincoli sul peering VNet globale e sui bilanciamenti del carico](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . È possibile usare il comando `curl` con `-v` opzione per risolvere eventuali problemi di connettività con gli endpoint pubblici o privati prima di usare l'oggetto.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Dall'intestazione del cluster o all'interno della rete virtuale di Azure con Apache Spark

Quando ci si connette direttamente dal nodo head del cluster o da una risorsa all'interno della stessa istanza di Rete virtuale di Azure del cluster HDInsight, è necessario usare la porta `10002` per il server Spark Thrift invece di `10001`. Nell'esempio seguente viene illustrato come connettersi direttamente al nodo head:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Prerequisiti per gli esempi

* Un cluster Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Si noti lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria del cluster. Ad esempio, `wasb://` per archiviazione di Azure, `abfs://` per Azure Data Lake Storage Gen2 o `adl://` per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per archiviazione di Azure, l'URI viene `wasbs://`. Per altre informazioni, vedere [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

* Opzione 1: un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Per la maggior parte dei passaggi di questo documento si presuppone che si stia usando una sessione SSH nel cluster.

* Opzione 2: un client di Oneline locale.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

Questo esempio si basa sull'uso del client con estensione da una connessione SSH.

1. Aprire una connessione SSH al cluster con il codice seguente. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connettersi a HiveServer2 con il client dell'utente corrente dalla sessione SSH aperta immettendo il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    È `!sql`, che viene usato per eseguire le istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In un nuovo cluster viene elencata solo una tabella: **hivesampletable**.

4. Usare il comando seguente per visualizzare lo schema di hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Questo comando restituisce le informazioni seguenti:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Tali informazioni descrivono le colonne nella tabella.

5. Immettere le istruzioni seguenti per creare una tabella denominata **log4jLogs** usando i dati di esempio forniti con il cluster HDInsight: (rivedere la richiesta in base allo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Queste istruzioni eseguono le azioni seguenti:

    |. |Descrizione |
    |---|---|
    |DROP TABLE|Se la tabella esiste, viene eliminata.|
    |CREA TABELLA ESTERNA|Crea una tabella **esterna** in hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.|
    |FORMATO DI RIGA|Modalità di formattazione dei dati. In questo caso, i campi in ogni log sono separati da uno spazio.|
    |ARCHIVIATO COME PERCORSO DI TEXTFILE|Dove vengono archiviati i dati e in quale formato di file.|
    |SELECT|seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]** . Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.|
    |INPUT__FILE__NAME LIKE '%. log '|Hive tenta di applicare lo schema a tutti i file nella directory. In questo caso, la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive di restituire dati solo da file che terminano con .log.|

   > [!NOTE]  
   > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'operazione MapReduce.
   >
   > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    L'output di questo comando è simile al testo seguente:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Esci da:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Eseguire un file HiveQL

Si tratta di una continuazione dell'esempio precedente. Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un file denominato **query.hql**:

    ```bash
    nano query.hql
    ```

1. Usare il testo seguente come contenuto del file. Questa query crea una nuova tabella "interna" denominata **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Queste istruzioni eseguono le azioni seguenti:

    |. |Descrizione |
    |---|---|
    |CREATE TABLE SE NON ESISTE|Se la tabella non esiste già, viene creata. Poiché non viene usata la parola chiave **External** , questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono totalmente gestite da Hive.|
    |ARCHIVIATO COME ORC|archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.|
    |INSERISCI SOVRASCRITTURA... Selezionare|seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]** , poi inserisce i dati nella tabella **errorLogs**.|

    > [!NOTE]  
    > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

1. Per salvare il file, usare **Ctrl**+**X**, quindi immettere **Y**e infine **premere invio**.

1. Usare il codice seguente per eseguire il file tramite Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Il parametro `-i` avvia Beeline ed esegue le istruzioni nel file `query.hql`. Dopo il completamento della query, viene visualizzato il prompt `jdbc:hive2://headnodehost:10001/>`. È anche possibile eseguire un file usando il parametro `-f`, che chiude Beeline dopo il completamento della query.

1. Per verificare che la tabella **errorLogs** sia stata creata, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Installare il client di Oneline

Sebbene sia incluso nei nodi head del cluster HDInsight, è consigliabile installarlo in un computer locale.  I passaggi seguenti per l'installazione di un computer locale sono basati su un [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aggiornare gli elenchi di pacchetti. Immettere il comando seguente nella shell bash:

    ```bash
    sudo apt-get update
    ```

1. Installare Java se non è installato. È possibile verificare con il comando `which java`.

    1. Se non è installato alcun pacchetto Java, immettere il comando seguente:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Aprire il file bashrc (in genere disponibile in ~/.bashrc): `nano ~/.bashrc`.

    1. Modificare il file bashrc. Aggiungere la riga seguente alla fine del file:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Quindi premere **CTRL + X**, quindi **Y**, quindi INVIO.

1. Scaricare Hadoop e gli archivi di Oneline, immettere i comandi seguenti:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Decomprimere gli archivi, immettere i comandi seguenti:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modificare ulteriormente il file bashrc. È necessario identificare il percorso in cui gli archivi sono stati decompressi. Se si usa il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/install-win10)e sono stati seguiti i passaggi esatti, il percorso sarà `/mnt/c/Users/user/`, dove `user` è il nome utente.

    1. Aprire il file: `nano ~/.bashrc`

    1. Modificare i comandi seguenti con il percorso appropriato e quindi immetterli alla fine del file bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Quindi premere **CTRL + X**, quindi **Y**, quindi INVIO.

1. Chiudere e riaprire la sessione bash.

1. Testare la connessione. Usare il formato di connessione da [endpoint pubblici o privati](#over-public-or-private-endpoints), sopra.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni generali su hive in HDInsight, vedere [usare Apache hive con Apache Hadoop in HDInsight](hdinsight-use-hive.md)

* Per altre informazioni su altri modi per lavorare con Hadoop in HDInsight, vedere [usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

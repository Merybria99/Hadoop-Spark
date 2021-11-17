# Appunti per la esecuzione di esercizi su hdfs

## Avviare il cluster
Porsi nella cartella del cluster e avviarlo attraverso il comando 

```bash
    docker-compose up
```
il quale avvia il cluster. Si consiglia di lasciare la finestra corrente aperta e di aprirne una ulteriore per permettere il corretto funzionamento della gestione dei dati nel cluster.

### **Connessione al nodo master per Hadoop**
Nella seconda finestra aperta del terminale viene effettuata la connessione al nodo master.

```bash
    docker exec -it master bash
```
Se effettuo il comando **ls** vengono visualizzate delle cartelle memorizate nel nodo master.

### **Connessione al nodo master per Spark**
Nella seconda finestra aperta del terminale viene effettuata la connessione al nodo master.

```bash
    docker exec -it sp_master bash
```
Se effettuo il comando **ls** vengono visualizzate delle cartelle memorizate nel nodo master.
Per permettere l'accesso alla cartella **testfiles** nella qual sono inseriti i **file jar da eseguire e gli script**.

# Spark
Creare tramite l'IDE il file jar da eseguire e **salvare il file all'interno della cartella testfiles** del cluster.

Avviare poi il cluster tramite le istruzioni sopra riportate e effettuare le seguenti istruzioni all'interno del **terminale del nodo master**.

Per l'esecuzione bisogna avviare il comando
```bash
    cd 
    cd testfiles
```
Prima di effettuare il run del programma è necessario creare un file bash che permetta di avviare correttamente quello che è il jar prodotto per Spark.

Del bash è necessario applicare delle modifiche **alla definizione del path del package** e al **nome del file jar** da eseguire.
```bash
    /opt/bitnami/spark/bin/spark-submit \
    --class **it.unisa.diem.hpc.spark.wordcount.SparkDriver** \
    --master local \
    /testfiles/**WordCountSpark.jar** \
    /testfiles/input /testfiles/output
```
Cambiare le sezioni circondate da ** **.

Dopo la variazione è poi possibile avviare il file bash, quindi il jar,con il comando:
```bash
    . launch_single.sh
```
## Esecuzione per IDE Intellij

### Setup ambiente Maven
Nel file **pom.xml** devono essere impostate le dipendenze tra il file sia con **Spark** che con **Scala**

```xml
    <dependencies>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>2.12.15</version>
        </dependency>

        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.12</artifactId>
            <version>3.2.0</version>
        </dependency>
    </dependencies>
```
Ricordado di adoperare una versione del JDK **11**.

### Setup ambiente per la esecuzione in locale 

Innanzitutto si deve modificare il progetto introducendo una **cartella input** all'interno della quale bidogna inserire il file da processare.

Impostare i parametri a linea di comando con **Run->Edit Configuration... ->** e nella sezione parameters aggiungere **input output**.

Al momento della istanziazione dell'oggetto **SparkConf** è necessario specificare dei metodi aggiuntivi per l'esecuzione in locale.

```java
    SparkConf conf = new SparkConf().setAppName("Spark Project").setMaster("local[1]").set("spark.executor.memory", "1g");
```

Successivamente verrà prodotta una cartella **output** nella quale verrà inserito il file di output.


### **Caso errore permission denied**
Nel caso in cui durante l'esecuzione si incontri un errore del tipo **permissioni denied** allora è necessario dare tutti i permessi di sistema alla cartella testfiles.

Mi pongo quindi nella cartella del cluster Spark ed effettuo il comando:
```bash
    sudo chmod 777 -R .*
```


# Hadoop
Avviare poi il cluster tramite le istruzioni sopra riportate e effettuare le seguenti istruzioni all'interno del **terminale del nodo master**.

### Dare al nodo master la leggittimità della sua autorità

Una volta entrati nel nodo master effettuo il comando 
```bash
    hdfs namenode -format
```
Il contenitore master diventa master del processo distribuito (hadoop)

### Lanciare hdfs 
Per permettere di lanciare il programma hadoop 
```bash
    $HADOOP_HOME/sbin/start-dfs.sh
```
```bash
    $HADOOP_HOME/sbin/start-yarn.sh
```

## Step per l'esecuzione su cluster del file jar

Inserimento nella cartella **hddata** il file txt e il file jar
``` bash
    cd /data
```
``` bash
    hdfs dfs -put document.txt hdfs:///input
```
``` bash
    hadoop jar nomeJar.jar /input /output
```

#### **Prendere visione dei risultati**

``` bash
    hdfs dfs -cat /output/part-r-00000
```

### Comandi per ripulire il file system di hadoop

``` bash
    hdfs dfs -rm -r hdfs:///input
```
``` bash
    hdfs dfs -rm -r hdfs:///output
```

## Esecuzione per IDE Intellij
Nel file **pom.xml** devono essere impostate le dipendenze tra il file ed **Hadoop**

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-core</artifactId>
            <version>1.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>3.3.1</version>
        </dependency>
    </dependencies>
```
Ricordado di adoperare una versione del JDK **1.8** corretto.

### Setup ambiente per la esecuzione in locale 

Innanzitutto si deve modificare il progetto introducendo una **cartella input** all'interno della quale bidogna inserire il file da processare.

Impostare i parametri a linea di comando con **Run->Edit Configuration... ->** e nella sezione parameters aggiungere **input output**.

Una volta effettuato il caricamento dei dati è necessario effettuare il **run** del progetto.

Successivamente verrà prodotta una cartella **output** nella quale verrà inserito il file di output.
























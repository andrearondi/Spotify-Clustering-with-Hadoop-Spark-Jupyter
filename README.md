# 🎶 Spotify-Clustering-with-Hadoop-Spark-Jupyter
This project, developed for the course **"Data Intensive Computing"** at KTH University,  sets up a mini Hadoop + Spark cluster using Docker, and lets you analyze a Spotify dataset using PySpark in Jupyter Notebook.

---

## 📚 Prerequisites

Make sure you have the following installed:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Git](https://git-scm.com/)
- Terminal (Command Prompt, PowerShell, Terminal.app, iTerm, etc.)

Create a Docker account and make sure to complete the email verification step.

---

## 🔧 Setup

### 1. Clone the Repository

```bash
git clone https://github.com/andrearondi/Spotify-Clustering-with-Hadoop-Spark-Jupyter.git
cd Spotify-Clustering-with-Hadoop-Spark-Jupyter
```

### 2. Check ports (Windows & macOS)

> Windows (PowerShell):
```powershell
netstat -ano | findstr :8889
netstat -ano | findstr :9870
netstat -ano | findstr :8080
```

> macOS
```bash
lsof -i :8889
lsof -i :9870
lsof -i :8080
```

If no output is printed, then the port is free and not being used by any process.
If a port is in use, change the left side of the mapping in docker-compose.yml (e.g. use 8890:8888 for Jupyter).

### 3. Start Docker Desktop
Find Docker Desktop App, launch it and verify that it is running with:
```bash
docker info
```
If it prints a bunch of info about the Docker system, you can move on.

### 4. Start the Cluster

```bash
docker-compose up -d
```

### 5. Verify Network & Namenode
Make sure all services are running and the custom Docker network is created:

```bash
docker network ls
docker ps
```

### 6. Enter the Jupyter container:

```bash
docker exec -it jupyter bash
```

From there, you can test connection to Namenode:
```bash
curl http://namenode:9870
```
Once you are done, you can exit the Jupyter container and proceed with the next step (just type "exit")

---

## 📂 Upload Dataset to HDFS
Open a terminal inside the Hadoop NameNode container:

```bash
docker exec -it namenode bash
```

Copy the dataset into HDFS:

```bash
hdfs dfs -mkdir -p /spotify
hdfs dfs -put /data/dataset.csv /spotify/dataset.csv
```

Check that it’s in HDFS:

```bash
hdfs dfs -ls /spotify
```

---

## 📓 Access Jupyter Notebook

Open Jupyter in your browser:

```
http://localhost:8888
```

If needed, get the token:

```bash
docker logs jupyter 2>&1 | grep token
```

The notebook files are in the notebooks/ folder.

---

## ✨ Sample PySpark Code

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("SpotifyClustering") \
    .config("spark.hadoop.fs.defaultFS", "hdfs://namenode:9000") \
    .getOrCreate()

df = spark.read.csv("hdfs://namenode:9000/spotify/dataset.csv", header=True, inferSchema=True)
df.show(5)
```

---

## 🧹 Shut Down the Cluster

To stop and remove containers:

```bash
docker-compose down
```

---

## 📎 Tips & Troubleshooting

- If namenode is not resolvable from Jupyter, make sure the containers are on the same Docker network (hadoop).
- Use curl http://namenode:9870 from the Jupyter container to verify connectivity.
- If dataset loads as HTML, make sure you didn’t clone a web view (like from Hugging Face) — download the raw file directly.

---

## Credits

- Hadoop Docker: https://github.com/big-data-europe/docker-hadoop
- Dataset: https://huggingface.co/datasets/maharshipandya/spotify-tracks-dataset

---

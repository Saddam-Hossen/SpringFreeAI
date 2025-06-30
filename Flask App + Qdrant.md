Here's a complete **GitHub-style documentation** for installing and testing a simple RAG (Retrieval Augmented Generation) system using **Flask**, **Flask-CORS**, **sentence-transformers**, and **Qdrant**.

---

## 🔧 Setup: Local RAG with Flask + Qdrant + Sentence Transformers
<details>
<summary><strong>Step-by-Step: Run Qdrant with Docker</strong></summary>
   To **run Qdrant** locally, follow the steps below. This guide assumes you're using **Docker**, which is the easiest way to run Qdrant.

---

## ✅ Step-by-Step: Run Qdrant with Docker

### 🔹 1. **Ensure Docker is installed and running**

Check Docker version:

```bash
docker --version
```

If not installed, install Docker from:
👉 [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)

---

### 🔹 2. **Run Qdrant using Docker**

```bash
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant
```

* `6333` → REST API port
* `6334` → gRPC port
* `qdrant/qdrant` → Official Qdrant image from Docker Hub

You should see output like:

```
Qdrant HTTP listening on 6333
Qdrant gRPC listening on 6334
Access web UI at http://localhost:6333/dashboard
```

✅ Now Qdrant is live at:
👉 [http://localhost:6333/dashboard](http://localhost:6333/dashboard)

---

### 🔹 3. **Optional: Run in background**

To keep Qdrant running in the background:

```bash
docker run -d -p 6333:6333 -p 6334:6334 qdrant/qdrant
```

---

## 🔄 Restarting or Stopping Qdrant

To stop:

```bash
docker ps   # copy container ID for qdrant
docker stop <container_id>
```

To restart:

```bash
docker start <container_id>
```

---

Let me know if you want:

* To **persist Qdrant data** (use Docker volume)
* Use **Qdrant without Docker**
* Deploy to **cloud server**

  
</details>
<details>
<summary><strong>My practise</strong></summary>
   <project>

     saddamnvn@DESKTOP-PTTGD06:~$ python3 -m venv myenv
     saddamnvn@DESKTOP-PTTGD06:~$ source myenv/bin/activate
     (myenv) saddamnvn@DESKTOP-PTTGD06:~$ pip install flask flask-cors sentence-transformers
     (myenv) saddamnvn@DESKTOP-PTTGD06:~$ mkdir flask-qdrant  cd flask-qdrant 
     (myenv) saddamnvn@DESKTOP-PTTGD06:~/flask-qdrant$ nano app.py
     
    
      
   </project>

    <project>
              from flask import Flask, request, jsonify
              from flask_cors import CORS
              from sentence_transformers import SentenceTransformer
              import requests
              import numpy as np
              
              app = Flask(__name__)
              CORS(app)
              
              # Load sentence transformer model
              model = SentenceTransformer('all-MiniLM-L6-v2')
              
              # Qdrant settings
              QDRANT_URL = "http://localhost:6333"
              COLLECTION_NAME = "device-data"
              VECTOR_SIZE = 384  # for 'all-MiniLM-L6-v2'
              
              # Initialize Qdrant collection (only once)
              @app.before_request
              def init_qdrant_once():
                  if not hasattr(app, 'qdrant_initialized'):
                      response = requests.put(f"{QDRANT_URL}/collections/{COLLECTION_NAME}", json={
                          "vectors": {"size": VECTOR_SIZE, "distance": "Cosine"}
                      })
                      print("Qdrant init:", response.json())
                      app.qdrant_initialized = True
              
              
              @app.route("/add", methods=["POST"])
              def add_data():
                  data = request.json.get("text")
                  if not data:
                      return jsonify({"error": "Missing 'text' in request"}), 400
              
                  embedding = model.encode(data).tolist()
              
                  payload = {
                      "points": [{
                          "id": np.random.randint(1, 1_000_000),
                          "vector": embedding,
                          "payload": {"text": data}
                      }]
                  }
              
                  response = requests.put(f"{QDRANT_URL}/collections/{COLLECTION_NAME}/points", json=payload)
                  return jsonify(response.json())
              
              
              @app.route("/search", methods=["GET"])
              def search():
                  query = request.args.get("query")
                  if not query:
                      return jsonify({"error": "Missing 'query'"}), 400
              
                  embedding = model.encode(query).tolist()
              
                  search_payload = {
                      "vector": embedding,
                      "top": 5,
                      "with_payload": True
                  }
              
                  response = requests.post(
                      f"{QDRANT_URL}/collections/{COLLECTION_NAME}/points/search",
                      json=search_payload
                  )
              
                  return jsonify(response.json())
              
              
              if __name__ == "__main__":
                  app.run(debug=True)
      
   </project>

   <project>

        (myenv) saddamnvn@DESKTOP-PTTGD06:~/flask-qdrant$ source ../myenv/bin/activate
         // 🧪 Test the API
         // Add a document:
        curl -X POST http://localhost:5000/add -H "Content-Type: application/json" -d '{"text": "Device Management — Lifecycle Manager for IT assets"}'

       // Search documents:
       curl "http://localhost:5000/search?query=device manager"

        // ✅ How to Test with curl
          1. Add a text:
          curl -X POST http://127.0.0.1:5000/add \
            -H "Content-Type: application/json" \
            -d '{"text": "Device Management — Lifecycle Manager for IT assets"}'
          2. Search for similar content:
          curl "http://127.0.0.1:5000/search?query=manager"

   </project>
   
  
</details>

<details>
<summary><strong>tep-by-Step: Run Flask App</strong></summary>
    To **run your Flask app** (e.g., `app.py`) after installing all dependencies and activating your virtual environment, follow these steps:

---

## ✅ Step-by-Step: Run Flask App

### 🔹 1. **Navigate to your project directory**

```bash
cd ~/flask-qdrant
```

---

### 🔹 2. **Activate your virtual environment**

If you created it like this earlier:

```bash
python3 -m venv myenv
```

Then activate it:

```bash
source ../myenv/bin/activate
```

Or if it's inside the project:

```bash
source myenv/bin/activate
```

You should now see `(myenv)` at the beginning of your terminal.

---

### 🔹 3. **Run the Flask App**

```bash
python app.py
```

You should see:

```
* Running on http://127.0.0.1:5000
```

This means your Flask app is running locally.

---

## 🌐 Test in Browser or Postman

* Visit `http://127.0.0.1:5000/` → Should return `"Flask-Qdrant is running!"`

* Add data:

```bash
curl -X POST http://127.0.0.1:5000/add \
  -H "Content-Type: application/json" \
  -d '{"text": "Device Management — Lifecycle Manager for IT assets"}'
```

* Search:

```bash
curl "http://127.0.0.1:5000/search?query=device manager"
```

---

Let me know if you want to run this **in the background**, **as a service**, or expose to the internet (via **ngrok** or other means).

  
</details>

> Use this to embed your text and search it using semantic similarity via Qdrant vector database.

---

### 📁 Project Structure

```
flask-qdrant/
│
├── app.py                 # Main Flask application
├── requirements.txt       # Python dependencies
├── README.md              # This documentation
```

---

### ✅ Prerequisites

* Python 3.8+
* Docker installed (for running Qdrant)
* Git (optional)
* MongoDB (optional if you want to integrate your data later)

---

### 🐍 Step 1: Clone and Set Up Virtual Environment

```bash
git clone https://github.com/your-repo/flask-qdrant.git
cd flask-qdrant

# Create virtual environment
python3 -m venv myenv
source myenv/bin/activate

# Install required packages
pip install -r requirements.txt
```

> If you're using Ubuntu and see an "externally-managed-environment" error, make sure you activate the virtualenv correctly as shown above.

---

### 📦 requirements.txt

```txt
flask
flask-cors
sentence-transformers
qdrant-client
```

---

### 🧠 Step 2: Start Qdrant via Docker

```bash
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant
```

Access Qdrant dashboard:

```
http://localhost:6333/dashboard
```

---

### 🚀 Step 3: Create `app.py`

```python
from flask import Flask, request, jsonify
from flask_cors import CORS
from sentence_transformers import SentenceTransformer
from qdrant_client import QdrantClient
from qdrant_client.http.models import Distance, VectorParams, PointStruct

app = Flask(__name__)
CORS(app)

# Load model
model = SentenceTransformer('all-MiniLM-L6-v2')

# Init Qdrant client and collection
client = QdrantClient("localhost", port=6333)
collection_name = "snvn-knowledge"

client.recreate_collection(
    collection_name=collection_name,
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
)

@app.route("/")
def home():
    return "Flask-Qdrant is running!"

@app.route("/add", methods=["POST"])
def add():
    data = request.get_json()
    text = data["text"]
    vector = model.encode(text).tolist()

    client.upsert(
        collection_name=collection_name,
        points=[PointStruct(id=1, vector=vector, payload={"text": text})]
    )
    return jsonify({"status": "inserted"})

@app.route("/search", methods=["GET"])
def search():
    query = request.args.get("query")
    vector = model.encode(query).tolist()

    results = client.search(
        collection_name=collection_name,
        query_vector=vector,
        limit=5
    )

    return jsonify([
        {"score": r.score, "text": r.payload.get("text", "")}
        for r in results
    ])

if __name__ == "__main__":
    app.run(debug=True)
```

---

### 🧪 Step 4: Test the API

#### ➕ Add text:

```bash
curl -X POST http://127.0.0.1:5000/add \
  -H "Content-Type: application/json" \
  -d '{"text": "Device Management — Lifecycle Manager for IT assets"}'
```

#### 🔍 Search:

```bash
curl "http://127.0.0.1:5000/search?query=manager"
```

---

### 📚 Optional: Integrate with MongoDB

You can fetch all documents from MongoDB, loop over them, embed, and push to Qdrant. Ask if you want a full script.

---

### ✅ Done

You now have:

* Flask app
* Local embeddings via sentence-transformers
* Semantic vector search with Qdrant

Let me know if you want to deploy this, or call this `/search` endpoint from Spring Boot OpenAI chat prompt.

---

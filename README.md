# ⚡️ DynamicKV: Scalable Web-Based Key-Value Store

**A Lightweight NoSQL Engine with Adaptive Consistency and Real-Time Synchronization**

DynamicKV began as a college DBMS project and evolved into a lightweight, high-performance NoSQL key-value store. Written in modern **C++17**, it features a custom-made Robin Hood Hashing-based storage engine and exposes data over HTTP via a **REST API**.

You can run DynamicKV as a standalone binary or easily integrate it into your own services. It's built for speed, scalability, and ease of use.

## 🚀 Key Features

* **Model-Based Storage:** Store any "model" (e.g., `users`, `products`) in its own dedicated directory under `data/`.
* **Segmented On-Disk Files:** For high performance and manageability, data is segmented into rolling files:
    * `.kv`: Append-only record log.
    * `.idx`: In-disk index of key $\to$ offset pairs.
    * `.bf`: Bloom filter for extremely fast "not present" checks.
* **Tunable Performance:** Easily adjust segment sizing via `config/db.conf`.
* **In-Memory Hot Cache:** Utilizes custom Robin-Hood Hashing for an efficient cache of frequently accessed keys.
* **Thread-Safe Operations:** Guarantees safe `append`, `lookup`, and `delete` operations using `std::thread` and `std::mutex`.
* **Pure C++ REST API:** Exposes data via a lightweight Crow web framework—no external database is required.

---

## 📦 Tech Stack

| Category | Component | Description |
| :--- | :--- | :--- |
| **Core** | C++17, STL | High-performance backend engine. |
| **Concurrency** | `std::thread`, `std::mutex` | Thread-safe operations and concurrency. |
| **Hashing** | Custom HashMap | Robin-Hood Hashing implementation for efficiency. |
| **Networking** | Crow | Header-only, Flask-style RESTful server. |
| **Build & CLI** | GNU `Makefile`, `g++`, `fmt` | Standard build tools and modern formatting library. |
| **Configuration** | `nlohmann::json` | Simple JSON-based configuration management. |

---

## 🏁 Quickstart








This executes the following build command:

g++ -std=c++17 -O2 main.cpp config.cpp bloomfilter.cpp segment.cpp segment_mgr.cpp storage_engine.cpp thread_pool.cpp -Iinclude -lfmt -pthread -o dynamickv

Alternatively, download a prebuilt binary from the Releases page and unpack it.

2. Configure

Edit the settings in config/db.conf to customize the database behavior.

Default config/db.conf:

JSON
{ 
  "data_dir": "./data", 
  "segment_size_mb": 64, 
  "file_extension": ".kv", 
  "index_extension": ".idx", 
  "bloom_extension": ".bf", 
  "bloom_bits_kb": 8, 
  "bloom_hashes": 4, 
  "thread_pool_size": 4 
}
The data_dir specifies where your per-model folders (e.g., users/, products/) will reside. Bloom filter parameters and segment sizing are configured here

Execute the compiled binary:
./dynamickv

API Documentation
All endpoints use JSON for requests and responses. The Base URL is http://localhost:8008/.

Method	Path	Body (JSON)	Description
GET	/	—	List all available models (subdirectories).
POST	/{model}/{key}	{ "key": "...", ...fields }	Create/Update: Creates the model if it doesn't exist. Upserts the JSON object for the specified key.
GET	/{model}	—	List: Get all key → value pairs within the specified model.
GET	/{model}/{key}	—	Retrieve: Get the single JSON object associated with key in the model.
DELETE	/{model}	—	Delete Model: Delete the entire model and all associated on-disk files.
DELETE	/{model}/{key}	—	Delete Key: Delete one key-value record from the model.

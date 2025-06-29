## <strong>Spring Boot AI Chat & Image Generation with GPT4All & Stable Diffusion on AWS</strong>
* ✅ **Spring Boot** (Java backend)
* 🤖 **GPT4All** (for local text understanding/chat)
* 🖼️ **Stable Diffusion** (for local image generation)
* 🚀 **AWS support** for remote access
* 🔐 Includes security notes and full installation via command line

---

````markdown
# 🧠 Spring Boot AI Assistant: GPT4All + Stable Diffusion (Offline + AWS Ready)

A full-stack **offline AI backend** using Java Spring Boot, integrating:

- 🤖 **GPT4All**: Local Large Language Model (LLM) for natural language understanding and text generation
- 🎨 **Stable Diffusion**: Local image generator for converting text prompts into AI-generated images

You can run this **entirely offline on your local machine** or deploy it on **AWS EC2** to make it globally accessible.

---

## ✨ Features

- Fully local AI (no cloud APIs required)
- Uses GPT4All for dynamic prompt and intent processing
- Generates AI images with Stable Diffusion
- Automatically decides whether to chat or generate image
- Spring Boot REST API
- Deployable on **AWS EC2** for public access

---

## 🧰 Tech Stack

| Tool             | Role                           |
|------------------|--------------------------------|
| Spring Boot      | Backend API                    |
| GPT4All          | Local language model (chat, prompts) |
| Stable Diffusion | Local image generator          |
| Java 17+         | Programming language            |
| Ubuntu / AWS EC2 | Hosting platform                |

---

## 🚀 Setup & Installation (Ubuntu)

### ✅ 1. Install GPT4All (CLI Binary)

```bash
wget https://gpt4all.io/releases/gpt4all-lora-linux-x86.zip
unzip gpt4all-lora-linux-x86.zip
chmod +x gpt4all-lora-linux-x86
./gpt4all-lora-linux-x86  # test
````

Optional: Run [gpt4all-api](https://github.com/nomic-ai/gpt4all/tree/main/apps/api) for HTTP access.

---

### 🎨 2. Install Stable Diffusion (AUTOMATIC1111)

```bash
sudo apt update && sudo apt install git python3 python3-venv python3-pip -y

git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
cd stable-diffusion-webui

mkdir -p models/Stable-diffusion
# Download model (e.g. Realistic_Vision_V5.1_noVAE.safetensors) and place in models/Stable-diffusion

python3 launch.py --api
```

Web UI: [http://127.0.0.1:7860](http://127.0.0.1:7860)
API Endpoint: `http://127.0.0.1:7860/sdapi/v1/txt2img`

---

### ☕ 3. Spring Boot Backend (Java)

#### ✅ GPT4AllService.java

```java
@Service
public class GPT4AllService {
    private final String GPT4ALL_BINARY = "/path/to/gpt4all-lora-linux-x86";

    public String ask(String prompt) throws IOException, InterruptedException {
        ProcessBuilder pb = new ProcessBuilder(GPT4ALL_BINARY, "-p", prompt, "-n", "128");
        Process process = pb.start();

        BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()));
        StringBuilder sb = new StringBuilder();
        String line;
        while ((line = reader.readLine()) != null) sb.append(line).append("\n");

        process.waitFor();
        return sb.toString().trim();
    }
}
```

#### 🎨 StableDiffusionService.java

```java
@Service
public class StableDiffusionService {
    private static final String API = "http://127.0.0.1:7860/sdapi/v1/txt2img";
    private HttpClient client = HttpClient.newHttpClient();

    public byte[] generateImage(String prompt) throws Exception {
        String json = "{\"prompt\": \"" + prompt.replace("\"", "\\\"") + "\", \"steps\": 30}";

        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(API))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(json))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        String base64 = response.body().split("\"data\":\\[\"")[1].split("\"\\]")[0];
        return Base64.getDecoder().decode(base64);
    }
}
```

#### 🧠 AiController.java

```java
@RestController
@RequestMapping("/api")
public class AiController {
    @Autowired private GPT4AllService gptService;
    @Autowired private StableDiffusionService sdService;

    @PostMapping("/ask")
    public ResponseEntity<?> ask(@RequestBody Map<String, String> body) {
        String msg = body.get("message").toLowerCase();

        try {
            if (msg.matches(".*\\b(draw|image|picture|generate|paint)\\b.*")) {
                String prompt = gptService.ask("Create a detailed prompt to generate image for: " + msg);
                byte[] image = sdService.generateImage(prompt);
                return ResponseEntity.ok()
                        .header(HttpHeaders.CONTENT_DISPOSITION, "inline; filename=image.png")
                        .contentType(MediaType.IMAGE_PNG)
                        .body(image);
            } else {
                String reply = gptService.ask(msg);
                return ResponseEntity.ok(Map.of("response", reply));
            }
        } catch (Exception e) {
            return ResponseEntity.status(500).body(Map.of("error", e.getMessage()));
        }
    }
}
```

---

## 🌐 Access from Outside (AWS EC2)

1. **Run your app** on EC2 with:

   ```properties
   server.address=0.0.0.0
   server.port=8080
   ```

2. **Security Group**:

   * Open inbound port 8080 (TCP)
   * Source: 0.0.0.0/0 or your IP only

3. **Access API**:
   `http://<your-ec2-ip>:8080/api/ask`

---

## 🔐 Security Notes

* Never expose Stable Diffusion port (7860) to the public
* Use reverse proxy (e.g., Nginx) with HTTPS in production
* Optionally add basic authentication or token-based auth

---

## 💬 Sample API Usage

```http
POST /api/ask
Content-Type: application/json

{
  "message": "Draw a robot playing guitar at sunset"
}
```

* 🖼️ Returns PNG image if prompt requests drawing
* 💬 Returns text if it's a normal question

---

## 📸 Example Output

* Input: `Draw a fantasy dragon flying over a castle`
* Output: ![example](https://via.placeholder.com/400x300?text=AI+Image+Here)

---

## 📜 License

MIT

---

## 🤝 Contributing

Pull requests and stars are welcome! ✨

---

## 🔗 Connect with Me

Feel free to connect on [LinkedIn](https://linkedin.com) or open an issue if you need help getting this running.

---

## 🧠 Powered By

* [GPT4All](https://gpt4all.io/)
* [Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
* [Spring Boot](https://spring.io/projects/spring-boot)

---

```



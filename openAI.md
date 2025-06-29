<details>
  <summary><strong>controller code:</strong></summary>
  <project>
    
    package com.device.DeviceManagement.controller.openAI;
    import org.springframework.ai.chat.client.ChatClient;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequestMapping("/api/openai")
    public class ChatController {
        private final ChatClient chatClient;
    
        public ChatController(ChatClient.Builder chatClientBuilder) {
            this.chatClient = chatClientBuilder.build();
        }
    
        @GetMapping("/chat")
        public String chat(@RequestParam String prompt) {
    
            return chatClient.prompt(prompt).call().content();
        }
    }
  </project>
</details>
<details>
  <summary><strong>application.properties</strong></summary>
  <project>
    
    ##spring.ai.openai.api-key=${OPENAI_API_KEY}
    #spring.ai.openai.chat.options.model=gpt-4o-mini
    #spring.ai.openai.chat.options.temperature=0.7
    #spring.ai.openai.chat.options.max-tokens=512
    #spring.ai.openai.chat.options.top-p=1.0
    
  </project>
  
</details>
<details>
  <summary><strong>pom.xm</strong></summary>
  <?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.3.0</version>  <!-- Use stable latest -->
		<relativePath/>
	</parent>

	<groupId>com.device</groupId>
	<artifactId>DeviceManagement</artifactId>
	<version>1.1</version>
	<name>DeviceManagement</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>21</java.version>
		<spring-ai.version>1.0.0-M4</spring-ai.version>
	</properties>


	<repositories>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.ai</groupId>
				<artifactId>spring-ai-bom</artifactId>
				<version>${spring-ai.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<dependencies>
		<!-- Spring Boot Starters with Logback excluded -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-mongodb</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

		<!--&lt;!&ndash; Log4j2 without version, managed by Spring Boot &ndash;&gt;
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-log4j2</artifactId>
		</dependency>-->

		<!-- Redis -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-pool2</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-cache</artifactId>
		</dependency>

		<!-- Lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.32</version>
			<scope>provided</scope>
		</dependency>

		<!-- CSV Parsing -->
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-csv</artifactId>
			<version>1.9.0</version>
		</dependency>

		<!-- PDF Handling -->
		<dependency>
			<groupId>com.itextpdf</groupId>
			<artifactId>itext-core</artifactId>
			<version>8.0.2</version>
			<type>pom</type>
		</dependency>
		<dependency>
			<groupId>com.itextpdf</groupId>
			<artifactId>layout</artifactId>
			<version>8.0.2</version>
		</dependency>
		<dependency>
			<groupId>commons-io</groupId>
			<artifactId>commons-io</artifactId>
			<version>2.11.0</version>
		</dependency>

		<!-- Email Handling -->
		<dependency>
			<groupId>com.sun.mail</groupId>
			<artifactId>jakarta.mail</artifactId>
			<version>2.0.1</version>
		</dependency>

		<!-- Devtools -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>

		<!-- Testing -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.jetbrains</groupId>
			<artifactId>annotations</artifactId>
			<version>24.0.1</version>
			<scope>compile</scope>
		</dependency>

		<!-- Actuator for /actuator endpoints -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<!-- Micrometer Prometheus registry -->
		<dependency>
			<groupId>io.micrometer</groupId>
			<artifactId>micrometer-registry-prometheus</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.ai</groupId>
			<artifactId>spring-ai-openai-spring-boot-starter</artifactId>
		</dependency>

	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<version>3.5.3</version>
			</plugin>
		</plugins>
	</build>

</project>

</details>

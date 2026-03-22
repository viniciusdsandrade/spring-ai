# CLAUDE.md — spring-ai-with-openai

## Visao geral

API REST Spring Boot que integra OpenAI (via Spring AI) para analise de compatibilidade entre curriculos e vagas de emprego. Inclui parsing de documentos (Tika), scoring hibrido (LLM + embeddings), geracao de e-mails por IA e envio SMTP.

## Stack

| Camada       | Tecnologia                              |
|--------------|-----------------------------------------|
| Linguagem    | Java 25                                 |
| Framework    | Spring Boot 3.5.7                       |
| IA           | Spring AI 1.1.0 + OpenAI (gpt-5.1)     |
| Build        | Maven (wrapper incluso)                 |
| Validacao    | Jakarta Bean Validation                 |
| Parsing docs | Apache Tika 3.2.3                       |
| E-mail       | Spring Mail (Gmail SMTP)                |
| Testes       | JUnit 5, Mockito, AssertJ               |

## Comandos

```bash
./mvnw clean install        # Build completo
./mvnw test                 # Rodar testes
./mvnw spring-boot:run      # Subir aplicacao (requer OPENAI_API_KEY)
```

## Variaveis de ambiente obrigatorias

| Variavel              | Descricao                         | Default          |
|-----------------------|-----------------------------------|------------------|
| `OPENAI_API_KEY`      | Chave da API OpenAI               | —                |
| `MAX_COMPLETION_TOKENS` | Limite de tokens por resposta   | 2048             |
| `MAIL_USERNAME`       | Usuario SMTP Gmail                | (ver properties) |
| `MAIL_PASSWORD`       | Senha de app Gmail                | (ver properties) |

## Arquitetura

```
com.restful.open.ai
├── config/          # Beans de configuracao (AiConfig, TikaConfig)
├── controller/      # REST endpoints
├── dto/             # Java records (request/response imutaveis)
├── service/         # Interfaces de servico
└── service/impl/    # Implementacoes concretas
```

**Padrao de camadas:** Controller -> Service (interface) -> ServiceImpl. Sem camada de persistencia (stateless).

## Convencoes de codigo

- **DTOs como Java Records** — imutaveis, sem getters/setters manuais.
- **Injecao por construtor** — nunca `@Autowired` em campo.
- **Interfaces para servicos** — toda logica de negocio tem interface + impl.
- **Prompts em portugues** — as instrucoes enviadas ao LLM sao em PT-BR.
- **Validacao com Jakarta** — `@NotBlank`, `@Email` nos DTOs de entrada.
- **Nomes de classes** — PascalCase; sufixos: `Service`, `ServiceImpl`, `Controller`, `Config`, `Request`, `Response`.
- **Pacote** — `com.restful.open.ai`.

## Testes

- Localizacao: `src/test/java/com/restful/open/ai/service/`
- Nomeacao: `{ClasseTestada}Test.java`
- Anotacoes: `@ExtendWith(MockitoExtension.class)`, `@Mock`, `@InjectMocks`, `@DisplayName`.
- Surefire roda com `-XX:+EnableDynamicAgentLoading` (necessario para Mockito no Java 21+).
- Cobrir: cenarios de null, excecoes de I/O, dimensoes incompativeis de embeddings, fallback de scoring.

## Regras para o assistente

- **KISS** — mudancas minimas, sem refactor nao solicitado.
- **Nao leia/imprima segredos** — `secrets/`, chaves privadas. Arquivos `.env` podem ser lidos/editados quando solicitado.
- **Rode testes** apos mudancas relevantes e reporte o resultado.
- **Commits descritivos** — prefixo `feat:`, `fix:`, `refactor:`, `test:`, `docs:`.
- **Portugues** — respostas, comentarios de codigo e prompts de IA em portugues.

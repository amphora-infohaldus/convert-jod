# convert-jod

Amphora fork of EugenMayer/officeconverter — LibreOffice-based document conversion REST service. Backup converter for convert-mso (which uses MS Office via documents4j for 100% compatible DOCX→PDF).

## Build

```bash
./gradlew build
make build        # Docker production + dev images
make start-prod   # Run production image on :8080
make test         # Integration tests in Docker
```

Stack: Kotlin 2.3, Spring Boot 3.5, JodConverter 4.4.11, Java 21, LibreOffice.

## API

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/conversion?format=pdf` | Convert uploaded file (multipart `file` field) |
| GET | `/conversion/ready` | Readiness check (200 OK when LibreOffice is up) |
| GET | `/actuator/health` | Spring Actuator health (added by Amphora) |

## Amphora Changes (vs upstream)

1. **HTML→PDF fix**: `document-formats.json` — changed HTML/XHTML `inputFamily` from TEXT to WEB, added `writer_web_pdf_Export` filter, added `htm` extension
2. **Spring Actuator**: Added `spring-boot-starter-actuator` dependency and `/actuator/health` endpoint

## Key Files

- `src/main/resources/document-formats.json` — format registry (contains the HTML fix)
- `src/main/kotlin/.../ConversionController.kt` — REST controller
- `src/main/resources/application.yml` — config (port 8080, multipart 20MB)
- `Dockerfile` — multi-stage build on jodconverter-runtime

## Deployment

Image: `ghcr.io/amphora-infohaldus/convert-jod:latest`
Built via: `AmphoraKubernetes/.github/workflows/build-images.yml` (manual dispatch)
K8s manifests: `AmphoraKubernetes/workloads/convert-jod/`

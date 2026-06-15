# Шаг 1: Сборка приложения
FROM golang:1.25.6-alpine AS builder

WORKDIR /app

# Исправлено: Копируем файлы модулей из правильного места
COPY src/go.mod src/go.sum* ./
RUN if [ -f go.mod ]; then go mod download; fi

# Исправлено: Копируем весь исходный код из src в текущую WORKDIR (/app)
COPY src/ .

ENV CGO_ENABLED=0
RUN GOOS=linux go build -ldflags="-w -s" -o /app/tracker main.go parcel.go

# Шаг 2: Финальный образ на scratch
FROM scratch

WORKDIR /app

COPY --from=builder /app/tracker .

# База теперь копируется корректно из корня контекста сборки
COPY db/tracker.db ./tracker.db

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

EXPOSE 8080

CMD ["./tracker"]
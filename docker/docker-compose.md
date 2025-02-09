
```
services:
  prometheus: #server
    image: prom/prometheus # Имя образа, можно заменить на нужный
    ports:
      - "9090:9090"  # Проброс порта (локальный:контейнерный)
    #environment:
      #var: value2   # Переменные окружения
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml  # Монтирование локальной папки в контейнер
      - prometheusdata:/prometheus  # Хранение данных вне контейнера
    restart: always  # Автоматический рестарт при сбое


  grafana:
    image: grafana/grafana  # Образ PostgreSQL
    #environment:
      #var: value2   # Переменные окружения
    volumes:
      - grafanadata:/var/lib/grafana  # Хранение данных вне контейнера
    restart: always  # Автоматический рестарт при сбое
    depends_on:
      - prometheus  # Контейнер запускается после db
    ports:
      - "3000:3000"  # Проброс порта (локальный:контейнерный)

  alertmanager:
    image: prom/alertmanager
    ports:
      - "9093:9093"
#    volumes:
#      - ../alertmanager.yaml:/etc/alertmanager/alertmanager.yaml  # Монтирование конфигурации Alertmanager
    restart: always
    depends_on:
      - prometheus  # Контейнер запускается после db

volumes:
  prometheusdata:  # Определение именованного тома для базы данных
  grafanadata:
```

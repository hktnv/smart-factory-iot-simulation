# 🏭 Endüstri 4.0: Akıllı Fabrika ve IoT Sensör Telemetri Simülasyonu

[![Java](https://img.shields.io/badge/Java-17+-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)](https://www.docker.com/)
[![Redis](https://img.shields.io/badge/Redis-In%20Memory-red.svg)](https://redis.io/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Relational-blue.svg)](https://www.postgresql.org/)

Bu proje, Kocaeli Üniversitesi Teknoloji Fakültesi Bilişim Sistemleri Mühendisliği **TBL324 - İleri Java Uygulamaları** dersi kapsamında geliştirilmiştir. Proje; üretim hattındaki makinelerin durumlarını, anlık IoT sensör verilerini (sıcaklık, titreşim vb.) ve arıza alarmlarını mikroservis mimarisi ile gerçek zamanlı olarak izleyen dağıtık bir sistem simülasyonudur.

---

## 🏗️ Sistem Mimarisi (Microservices & Gateway)

Proje monolitik bir yapı yerine, birbirinden izole çalışan, ölçeklenebilir ve API Gateway arkasında konumlandırılmış mikroservislerden oluşmaktadır.

```mermaid
graph TD
    %% İstemci Katmanı
    UI[🖥️ Custom / Mobil GUI <br> Fabrika Krokisi] -->|REST / JSON İstekleri| AG

    %% Gateway Katmanı
    subgraph "API Gateway (Trafik Yönetimi)"
        AG[🚪 Spring Cloud / Kong Gateway]
    end

    %% Servis Katmanı
    subgraph "Mikroservisler (İzole İş Mantığı)"
        MCS[⚙️ Machine Catalog Service <br> Makine Sicil ve Yönetim]
        TS[📡 Telemetry Service <br> IoT Sensör Veri Akışı]
        AS[🚨 Alert Service <br> Anomali ve Alarm Yönetimi]
    end

    %% Yönlendirmeler
    AG -->|/api/v1/machines| MCS
    AG -->|/api/v1/telemetry| TS
    AG -->|/api/v1/alerts| AS

    %% Veri Katmanı
    subgraph "Veritabanı Katmanı"
        PG[(🐘 PostgreSQL <br> İlişkisel Veriler)]
        RD[(🟥 Redis <br> Anlık Sensör Verisi)]
    end

    %% Veri Bağlantıları
    MCS -->|JDBC| PG
    TS -->|NoSQL| RD
    TS -.->|Eşik Aşımı Tetiklemesi| AS
    AS -->|Geçmiş Alarmlar| PG

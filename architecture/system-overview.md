# Архитектура системы

## Обзор

Cleaning Booking System - микросервисное приложение для бронирования клининговых услуг. Система построена на современных технологиях с акцентом на масштабируемость и безопасность.

## Компоненты

```mermaid
graph TD
    subgraph External["Публичные клиенты"]
        UserBrowser["Браузер"]
        Mobile["Мобильное приложение"]
    end

    subgraph Internal["Внутренние клиенты"]
        AdminPanel["Админ-панель"]
        DevOps["DevOps доступ"]
    end

    subgraph Ingress["Ingress Layer"]
        Traefik["Traefik"]
        CDN["CDN (S3 + YandexCDN)"]
    end

    subgraph Frontend["Фронтенды"]
        CoreFE["Next.js"]
        AdminFE["React Admin"]
        Blog["Astro Blog"]
    end

    subgraph Backend["Бэкенд сервисы"]
        Booking["booking-service"]
        User["user-service"]
        Notification["notification-service"]
        Analytics["analytics-service"]
        AdminMS["admin-service"]
    end

    subgraph Data["Данные и кэш"]
        PostgreSQL["PostgreSQL"]
        ClickHouse["ClickHouse"]
        Memcached["Memcached"]
        VictoriaMetrics["VictoriaMetrics"]
    end

    subgraph VPN["Безопасный доступ (WireGuard)"]
        WireGuard["WireGuard VPN"]
        VPN_Admin["Админ VPN-клиенты"]
        VPN_DevOps["DevOps VPN-клиенты"]
    end

    UserBrowser --> Traefik
    Mobile --> Traefik

    VPN_Admin --> WireGuard
    VPN_DevOps --> WireGuard
    WireGuard --> Traefik

    Traefik --> CoreFE
    Traefik --> Blog
    Traefik --> CDN

    WireGuard --> AdminFE
    WireGuard --> AdminMS

    Traefik --> Booking
    Traefik --> User

    CoreFE --> Booking
    CoreFE --> User
    AdminFE --> AdminMS
    AdminFE --> Analytics

    Booking --> User
    Booking --> Notification
    Booking --> Analytics
    AdminMS --> Analytics

    Booking --> PostgreSQL
    User --> PostgreSQL
    AdminMS --> PostgreSQL
    Analytics --> ClickHouse

    User --> Memcached
    Booking --> Memcached
    AdminMS --> Memcached

    Backend --> VictoriaMetrics

    WireGuard --> PostgreSQL
    WireGuard --> VictoriaMetrics
    WireGuard --> ClickHouse
```

### Фронтенд

- **Next.js** - основное приложение для клиентов
- **React** - панель администратора
- **Astro** - маркетинговый блог

### Бэкенд (Go микросервисы)

- **booking-service** - управление бронированиями
- **user-service** - пользователи и аутентификация
- **notification-service** - уведомления
- **analytics-service** - аналитика и отчеты

### Инфраструктура

- **Kubernetes** - оркестрация контейнеров
- **PostgreSQL** - основная база данных
- **Redis/Memcached** - кэширование
- **Traefik** - API gateway
- **WireGuard VPN** - безопасный доступ

## Схема взаимодействия

## Ключевые особенности

- **Микросервисная архитектура** - независимое масштабирование компонентов
- **Безопасность** - JWT аутентификация, VPN для админки
- **Мониторинг** - централизованные логи и метрики
- **CI/CD** - автоматизированное тестирование и деплой

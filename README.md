# Go-TicTacToe-Service

A robust, high-performance backend implementation of Tic-Tac-Toe featuring an "unbeatable" AI. Built with **Go** and powered by the **MiniMax algorithm**, this service demonstrates clean architecture, dependency injection, and RESTful API design.

## Features

* **Unbeatable AI**: Integrated MiniMax algorithm that evaluates all possible moves to ensure the AI never loses.
* **Modern DI**: Built using `uber-go/fx` for clean dependency injection and lifecycle management.
* **Stateful Sessions**: Concurrent game handling using a thread-safe `sync.Map` repository.
* **Graceful Shutdown**: Ensures all connections are closed properly when the server stops.
* **Strict Validation**: Server-side move validation to prevent cheating or illegal moves.


## Project Structure

```
.
├── cmd                     # Application entry point and Fx modules.
│   └── app
│       └── main.go
├── go.mod
├── go.sum
├── internal
│   ├── datasource          # In-memory storage (sync.Map).
│   │   ├── mapper.go
│   │   ├── model.go
│   │   ├── repository.go
│   │   └── storage.go
│   ├── di                  # Fx container
│   │   └── container.go
│   ├── domain              # Core business logic and MiniMax implementation.
│   │   ├── model
│   │   │   ├── area.go
│   │   │   └── game.go
│   │   └── service
│   │       ├── interface.go
│   │       ├── minimax.go
│   │       └── service.go
│   └── web                 # HTTP handlers, routing, and DTOs.
│       ├── handler.go
│       ├── mapper.go
│       └── model.go
├── README.md
└── README_RUS.md
```

## Tech Stack

| Component | Technology |
| :--- | :--- |
| **Language** | Go (Golang) 1.21+ |
| **Dependency Injection** | [Uber/fx](https://github.com/uber-go/fx) |
| **Routing** | [go-chi/chi](https://github.com/go-chi/chi) |
| **ID Generation** | [google/uuid](https://github.com/google/uuid) |

## Getting Started

### 1. Installation
Clone the repository and download dependencies:
```bash
git clone https://github.com/LeezyWannaFall/Go-TicTacToe-Service
cd Go-TicTacToe-Service
```
### 2. Running the Server
```bash
go run cmd/main.go
```
The server will start at ```http://localhost:8080```

## API Reference

The game flow follows a standard REST pattern: Initialize -> Play.

### 1. Start a New Game
Generates a unique game session and returns the initial board state.
- Endpoint: POST /game
- Curl Example:
```bash
curl -X POST http://localhost:8080/game
```
- Response:
```bash
{
  "id": "7eeb88f1-af2d-4e26-97c0-8301407126a5",
  "field": [[0,0,0],[0,0,0],[0,0,0]],
  "is_finished": false
}
```
### 2. Make a Move
Send the updated board state. The AI will calculate its response and return the board with two new moves (yours and the AI's).
- Endpoint: POST /game/{uuid}
- Curl Example:
```bash
curl -X POST http://localhost:8080/game/7eeb88f1-af2d-4e26-97c0-8301407126a5 -H "Content-Type: application/json" -d '{"field": [[1,0,0],[0,0,0],[0,0,0]]}'
```
- Success Response:
```bash
{
  "id": "7eeb88f1-af2d-4e26-97c0-8301407126a5",
  "field": [[1,0,0],[0,2,0],[0,0,0]],
  "is_finished": false,
  "winner": 0
}
```

## AI Strategy: MiniMax

The AI uses a recursive decision-making algorithm to explore the game tree.
- Maximizer (Player): Tries to reach a score of +1.
- Minimizer (AI): Tries to reach a score of −1.

By simulating every possible move until the end of the game, the AI consistently chooses the path that minimizes the player's maximum possible gain.
# 📱 Tarefas App — Flutter + SQLite

Aplicativo mobile de gerenciamento de tarefas desenvolvido com **Flutter**, utilizando **SQLite** (via `sqflite`) como banco de dados local. Projeto desenvolvido como trabalho prático da disciplina de Desenvolvimento Mobile.

---

## 📋 Sobre o Projeto

O app permite criar, visualizar, editar e excluir tarefas com suporte a categorias, prioridades e datas de vencimento. Todos os dados são persistidos localmente no dispositivo usando SQLite, sem necessidade de conexão com internet.

### Tela principal

```
┌─────────────────────────────┐
│  Minhas Tarefas             │
│  3 pendentes          [🔧]  │
│ ┌─────────────────────────┐ │
│ │ 🔍 Buscar tarefas...    │ │
│ └─────────────────────────┘ │
│  4 Total | 1 ✓ | 3 ⏳ | 25% │
│  ████░░░░░░░░░░░░░░░░░░░░  │
│  Todas | Pendentes | Feitas │
│ ┌─────────────────────────┐ │
│ │ Entregar relatório      │ │
│ │ 💼 Trabalho  🔴 Alta    │ │
│ └─────────────────────────┘ │
│             [+ Nova Tarefa] │
└─────────────────────────────┘
```

---

## ✨ Funcionalidades

- **Criar tarefas** com título, descrição, prioridade, categoria e data de vencimento
- **Listar tarefas** com filtros por status (todas, pendentes, concluídas) e por categoria
- **Busca em tempo real** pelo título ou descrição
- **Editar tarefas** existentes
- **Concluir/reabrir** tarefas com um toque no checkbox
- **Excluir** com gesto de swipe (deslize para a esquerda) e confirmação
- **Estatísticas** — total, concluídas, pendentes e barra de progresso
- **Persistência local** — dados salvos no dispositivo via SQLite

---

## 🗂️ Estrutura do Projeto

```
lib/
├── main.dart                    # Ponto de entrada da aplicação
├── models/
│   └── tarefa.dart              # Modelo de dados + enums Prioridade e Categoria
├── database/
│   └── database_helper.dart     # Singleton com todas as operações SQLite (CRUD)
├── screens/
│   ├── home_screen.dart         # Tela principal com lista de tarefas
│   └── formulario_screen.dart   # Tela de criação e edição de tarefas
├── widgets/
│   ├── tarefa_card.dart         # Card reutilizável para cada tarefa
│   └── estatisticas_widget.dart # Painel de métricas e progresso
└── theme/
    └── app_theme.dart           # Design system — paleta, cores e tema dark
```

---

## 🗄️ Banco de Dados — SQLite

O banco é gerenciado pela classe `DatabaseHelper` (padrão Singleton). A tabela principal é criada automaticamente na primeira execução.

### Esquema da tabela `tarefas`

| Coluna           | Tipo SQL    | Descrição                          |
|------------------|-------------|------------------------------------|
| `id`             | TEXT PK     | UUID v4 gerado no app              |
| `titulo`         | TEXT NOT NULL | Título da tarefa (obrigatório)   |
| `descricao`      | TEXT        | Descrição opcional                 |
| `concluida`      | INTEGER     | `0` = pendente / `1` = concluída  |
| `prioridade`     | INTEGER     | `0` baixa · `1` média · `2` alta  |
| `categoria`      | INTEGER     | `0–5` (trabalho, pessoal...)       |
| `dataCriacao`    | TEXT        | ISO 8601 — gerado automaticamente  |
| `dataVencimento` | TEXT NULL   | Data opcional selecionada pelo usuário |

### Operações implementadas

```dart
// INSERT — nova tarefa
await db.inserirTarefa(tarefa);

// SELECT — todas as tarefas
List<Tarefa> tarefas = await db.buscarTodas();

// SELECT WHERE — busca por texto
List<Tarefa> resultado = await db.buscarPorTexto('Flutter');

// SELECT WHERE — filtra por categoria
List<Tarefa> trabalho = await db.buscarPorCategoria(Categoria.trabalho);

// UPDATE — editar tarefa
await db.atualizarTarefa(tarefaEditada);

// UPDATE parcial — alternar conclusão
await db.alternarConclusao(id, true);

// DELETE — remover tarefa
await db.deletarTarefa(id);

// SELECT COUNT — estatísticas
Map<String, int> stats = await db.obterEstatisticas();
// { 'total': 4, 'concluidas': 1, 'pendentes': 3 }
```

---

## 🛠️ Tecnologias

| Tecnologia | Versão | Uso |
|---|---|---|
| Flutter | ≥ 3.0 | Framework de desenvolvimento mobile |
| Dart | ≥ 3.0 | Linguagem de programação |
| sqflite | ^2.3.0 | Banco de dados SQLite local |
| path | ^1.8.3 | Resolução do caminho do banco |
| intl | ^0.18.1 | Formatação de datas em pt_BR |
| uuid | ^4.2.1 | Geração de IDs únicos (UUID v4) |
| flutter_slidable | ^3.0.1 | Gesto de swipe nos cards |
| google_fonts | ^6.1.0 | Tipografia |

---

## 🚀 Como Executar

### Pré-requisitos

- [Flutter SDK](https://docs.flutter.dev/get-started/install) ≥ 3.0 instalado
- Android Studio, VS Code ou outro editor com plugin Flutter
- Emulador Android/iOS ou dispositivo físico conectado

### Passo a passo

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/tarefas-app.git
cd tarefas-app

# 2. Instale as dependências
flutter pub get

# 3. Verifique o ambiente
flutter doctor

# 4. Execute o app
flutter run
```

Para gerar o APK de release:

```bash
flutter build apk --release
# O arquivo estará em: build/app/outputs/flutter-apk/app-release.apk
```

---

## 🧩 Conceitos de Desenvolvimento Mobile Aplicados

**Persistência local com SQLite**
O `DatabaseHelper` encapsula toda a comunicação com o banco usando o padrão Singleton, garantindo uma única instância durante o ciclo de vida do app. O mapeamento entre objetos Dart e registros do banco é feito manualmente com `toMap()` e `fromMap()`.

**Arquitetura em camadas**
O projeto separa responsabilidades entre `models` (domínio), `database` (dados), `screens` (apresentação) e `widgets` (componentes), facilitando manutenção e testes.

**Gerenciamento de estado com setState**
A tela principal usa `StatefulWidget` com `setState` para reagir a inserções, edições e exclusões, recarregando os dados do banco a cada operação.

**Navegação entre telas**
A navegação usa `Navigator.push` com retorno de resultado (`pop(true)`), permitindo que a `HomeScreen` saiba quando recarregar os dados após o formulário ser salvo.

**Componentização de widgets**
`TarefaCard` e `EstatisticasWidget` são widgets reutilizáveis que recebem dados por parâmetro, seguindo o princípio de composição do Flutter.

**Design responsivo e orientação**
O app força orientação retrato com `SystemChrome.setPreferredOrientations` e usa `SafeArea` para respeitar notch e barras do sistema.

---

## 📁 Categorias e Prioridades

| Categoria | Ícone | Cor |
|---|---|---|
| Trabalho | 💼 | Azul |
| Pessoal | 👤 | Lilás |
| Estudo | 🎓 | Teal |
| Saúde | ❤️ | Verde |
| Financeiro | 💰 | Laranja |
| Outro | 🏷️ | Cinza |

| Prioridade | Cor |
|---|---|
| Baixa | Verde |
| Média | Amarelo |
| Alta | Vermelho |

---

## 📄 Licença

---

Desenvolvido como projeto acadêmico — Disciplina de Desenvolvimento Mobile.

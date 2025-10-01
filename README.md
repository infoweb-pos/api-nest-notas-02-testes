# Tutorial de Testes Automatizados em NestJS

Este tutorial apresenta conceitos e práticas de testes automatizados utilizando o framework NestJS, com exemplos baseados em uma API de gerenciamento de tarefas (ToDo).

---

## Parte 1: Introdução Teórica sobre Testes Automatizados

### 1.1 O que são Testes Automatizados?

Testes automatizados são scripts que verificam automaticamente se o código da aplicação funciona conforme esperado. Eles são executados por ferramentas específicas e não requerem intervenção manual durante a execução, proporcionando:

- **Confiabilidade**: Garantem que o código funciona como esperado
- **Rapidez**: Executam verificações muito mais rápido que testes manuais
- **Regressão**: Detectam quando alterações quebram funcionalidades existentes
- **Documentação Viva**: Servem como exemplos de uso do código
- **Refatoração Segura**: Permitem modificar código com confiança

### 1.2 Tipos de Testes

#### 1.2.1 Testes Unitários (Unit Tests)
- **Objetivo**: Testar componentes individuais isoladamente (serviços, controllers, etc.)
- **Escopo**: Menor possível - uma função, método ou classe
- **Características**:
  - Rápidos de executar
  - Não acessam banco de dados, rede ou sistema de arquivos
  - Usam mocks e stubs para isolar dependências
  - Arquivos terminam em `*.spec.ts`

**Exemplo**: Testar se um método de serviço retorna os dados corretos

#### 1.2.2 Testes de Integração (Integration Tests)
- **Objetivo**: Testar a interação entre múltiplos componentes
- **Escopo**: Verificar se módulos diferentes funcionam juntos
- **Características**:
  - Mais lentos que testes unitários
  - Podem acessar banco de dados ou outros serviços
  - Testam fluxos completos

#### 1.2.3 Testes End-to-End (E2E Tests)
- **Objetivo**: Testar a aplicação completa, simulando usuário real
- **Escopo**: Todo o sistema, incluindo todas as camadas
- **Características**:
  - Mais lentos de executar
  - Testam requisições HTTP reais
  - Verificam integração completa da aplicação
  - Arquivos terminam em `*.e2e-spec.ts`

**Exemplo**: Testar se uma requisição POST cria um registro no banco

### 1.3 Pirâmide de Testes

```
        /\
       /  \      E2E Tests (Poucos)
      /____\     - Testes completos
     /      \    - Mais lentos
    /________\   Integration Tests (Alguns)
   /          \  - Testes de fluxos
  /____________\ Unit Tests (Muitos)
                 - Testes isolados
                 - Mais rápidos
```

### 1.4 Ferramentas no NestJS

- **Jest**: Framework de testes JavaScript/TypeScript
- **Supertest**: Biblioteca para testar APIs HTTP
- **@nestjs/testing**: Utilitários do NestJS para criar módulos de teste

### 1.5 Padrão AAA (Arrange-Act-Assert)

Estrutura recomendada para organizar testes:

1. **Arrange** (Preparar): Configurar dados e mocks necessários
2. **Act** (Agir): Executar a ação que está sendo testada
3. **Assert** (Verificar): Verificar se o resultado é o esperado

### 1.6 Comandos de Teste

```bash
# Executar todos os testes unitários
npm test

# Executar testes em modo watch (observando mudanças)
npm run test:watch

# Executar testes com cobertura de código
npm run test:cov

# Executar testes E2E
npm run test:e2e

# Executar testes em modo debug
npm run test:debug
```

---

## Parte 2: Testes Unitários (*.spec.ts)

Os testes unitários no NestJS são escritos usando Jest e ficam ao lado dos arquivos que testam, com a extensão `.spec.ts`.

### 2.1 Estrutura Básica de um Teste Unitário

```typescript
import { Test, TestingModule } from '@nestjs/testing';

describe('NomeDoComponente', () => {
  let component: TipoDoComponente;

  beforeEach(async () => {
    // Arrange: Criar módulo de teste
    const module: TestingModule = await Test.createTestingModule({
      controllers: [...],
      providers: [...],
    }).compile();

    // Obter instância do componente
    component = module.get<TipoDoComponente>(TipoDoComponente);
  });

  describe('nomeDoMetodo', () => {
    it('deve fazer algo específico', () => {
      // Arrange: Preparar dados
      const input = 'dados de teste';
      
      // Act: Executar método
      const result = component.metodo(input);
      
      // Assert: Verificar resultado
      expect(result).toBe('resultado esperado');
    });
  });
});
```

### 2.2 Exemplo Real: Testando o AppController

**Arquivo**: `src/app.controller.spec.ts`

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { AppController } from './app.controller';
import { AppService } from './app.service';

describe('AppController', () => {
  let appController: AppController;

  beforeEach(async () => {
    const app: TestingModule = await Test.createTestingModule({
      controllers: [AppController],
      providers: [AppService],
    }).compile();

    appController = app.get<AppController>(AppController);
  });

  describe('getInfo', () => {
    it('deve retornar informações da API com status, version e description', () => {
      const result = appController.getInfo();
      
      expect(result).toHaveProperty('status');
      expect(result).toHaveProperty('version');
      expect(result).toHaveProperty('description');
      expect(result.status).toBe('online');
      expect(result.version).toBe('1.0.0');
      expect(result.description).toBe('Esta é API de tarefas (todos) da turma de Infoweb 2025.');
    });
  });
});
```

**Explicação do código**:

1. **Imports**: Importamos as ferramentas de teste do NestJS e os componentes a testar
2. **describe()**: Agrupa testes relacionados (test suite)
3. **beforeEach()**: Executado antes de cada teste para preparar o ambiente
4. **Test.createTestingModule()**: Cria um módulo isolado para testes
5. **it()**: Define um caso de teste específico
6. **expect()**: Afirmação que verifica se o resultado é o esperado

### 2.3 Testando Services com Dependências

Quando um serviço possui dependências (como repositórios do TypeORM), precisamos usar **mocks**:

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { TasksService } from './tasks.service';
import { Task } from './task.entity';
import { Repository } from 'typeorm';

describe('TasksService', () => {
  let service: TasksService;
  let repository: Repository<Task>;

  // Mock do repositório
  const mockRepository = {
    find: jest.fn(),
    findOne: jest.fn(),
    create: jest.fn(),
    save: jest.fn(),
    remove: jest.fn(),
  };

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        TasksService,
        {
          provide: getRepositoryToken(Task),
          useValue: mockRepository,
        },
      ],
    }).compile();

    service = module.get<TasksService>(TasksService);
    repository = module.get<Repository<Task>>(getRepositoryToken(Task));
  });

  describe('findAll', () => {
    it('deve retornar um array de tarefas', async () => {
      // Arrange
      const expectedTasks = [
        { id: 1, title: 'Tarefa 1', description: 'Descrição 1' },
        { id: 2, title: 'Tarefa 2', description: 'Descrição 2' },
      ];
      mockRepository.find.mockResolvedValue(expectedTasks);

      // Act
      const result = await service.findAll();

      // Assert
      expect(result).toEqual(expectedTasks);
      expect(mockRepository.find).toHaveBeenCalled();
    });
  });

  describe('findOne', () => {
    it('deve retornar uma tarefa pelo ID', async () => {
      // Arrange
      const taskId = 1;
      const expectedTask = { 
        id: taskId, 
        title: 'Tarefa 1', 
        description: 'Descrição 1' 
      };
      mockRepository.findOne.mockResolvedValue(expectedTask);

      // Act
      const result = await service.findOne(taskId);

      // Assert
      expect(result).toEqual(expectedTask);
      expect(mockRepository.findOne).toHaveBeenCalledWith({ 
        where: { id: taskId } 
      });
    });

    it('deve lançar NotFoundException quando tarefa não existe', async () => {
      // Arrange
      const taskId = 999;
      mockRepository.findOne.mockResolvedValue(null);

      // Act & Assert
      await expect(service.findOne(taskId)).rejects.toThrow(
        `Tarefa com ID ${taskId} não encontrada`
      );
    });
  });

  describe('create', () => {
    it('deve criar uma nova tarefa', async () => {
      // Arrange
      const createTaskDto = {
        title: 'Nova Tarefa',
        description: 'Descrição da tarefa',
      };
      const createdTask = { 
        id: 1, 
        ...createTaskDto,
        status: 'aberto',
        createdAt: new Date(),
        updatedAt: new Date(),
      };
      
      mockRepository.create.mockReturnValue(createdTask);
      mockRepository.save.mockResolvedValue(createdTask);

      // Act
      const result = await service.create(createTaskDto);

      // Assert
      expect(result).toEqual(createdTask);
      expect(mockRepository.create).toHaveBeenCalledWith(createTaskDto);
      expect(mockRepository.save).toHaveBeenCalledWith(createdTask);
    });
  });
});
```

### 2.4 Principais Matchers do Jest

```typescript
// Igualdade
expect(value).toBe(expected);           // Igualdade estrita (===)
expect(value).toEqual(expected);        // Igualdade profunda (objetos)

// Veracidade
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeNull();
expect(value).toBeUndefined();
expect(value).toBeDefined();

// Números
expect(value).toBeGreaterThan(3);
expect(value).toBeLessThan(5);
expect(value).toBeCloseTo(0.3);

// Strings
expect(string).toMatch(/pattern/);
expect(string).toContain('substring');

// Arrays
expect(array).toContain(item);
expect(array).toHaveLength(3);

// Exceções
expect(() => fn()).toThrow();
expect(() => fn()).toThrow(Error);
await expect(promise).rejects.toThrow();

// Mocks
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith(arg1, arg2);
expect(mockFn).toHaveBeenCalledTimes(2);
```

### 2.5 Boas Práticas para Testes Unitários

1. **Um teste, uma responsabilidade**: Cada teste deve verificar apenas uma coisa
2. **Testes independentes**: Um teste não deve depender de outro
3. **Nomes descritivos**: O nome do teste deve explicar o que está sendo testado
4. **Use mocks para dependências**: Isole o componente sendo testado
5. **Teste casos de sucesso e falha**: Inclua cenários positivos e negativos
6. **Mantenha testes simples**: Testes complexos são difíceis de manter

---

## Parte 3: Testes E2E (*.e2e-spec.ts)

Os testes End-to-End (E2E) testam a aplicação completa, simulando requisições HTTP reais. No NestJS, eles ficam na pasta `test/` e usam a biblioteca **Supertest**.

### 3.1 Estrutura Básica de um Teste E2E

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from './../src/app.module';

describe('NomeDoController (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    // Criar aplicação de teste
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  afterAll(async () => {
    // Limpar recursos
    await app.close();
  });

  it('/rota (GET)', () => {
    return request(app.getHttpServer())
      .get('/rota')
      .expect(200)
      .expect('Resposta esperada');
  });
});
```

### 3.2 Exemplo Real: Testando a Rota Principal

**Arquivo**: `test/app.e2e-spec.ts`

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from './../src/app.module';

describe('AppController (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  it('/ (GET) - deve retornar informações da API', () => {
    return request(app.getHttpServer())
      .get('/')
      .expect(200)
      .expect((res) => {
        expect(res.body).toHaveProperty('status', 'online');
        expect(res.body).toHaveProperty('version', '1.0.0');
        expect(res.body).toHaveProperty('description');
      });
  });
});
```

**Explicação do código**:

1. **AppModule**: Importa o módulo principal da aplicação (toda a aplicação)
2. **createNestApplication()**: Cria instância completa da aplicação
3. **app.init()**: Inicializa a aplicação
4. **request()**: Faz requisições HTTP usando Supertest
5. **expect()**: Verifica status HTTP e resposta

### 3.3 Testando API de Tarefas (CRUD Completo)

**Arquivo**: `test/tasks.e2e-spec.ts` (exemplo completo)

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication, ValidationPipe } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from './../src/app.module';
import { TaskStatus } from '../src/tasks/task.entity';

describe('TasksController (e2e)', () => {
  let app: INestApplication;
  let createdTaskId: number;

  beforeAll(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    
    // Aplicar mesmas configurações do main.ts
    app.useGlobalPipes(new ValidationPipe({
      transform: true,
      whitelist: true,
      forbidNonWhitelisted: true,
    }));
    
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  describe('/tasks (POST)', () => {
    it('deve criar uma nova tarefa', () => {
      return request(app.getHttpServer())
        .post('/tasks')
        .send({
          title: 'Tarefa de Teste E2E',
          description: 'Descrição da tarefa de teste',
          status: TaskStatus.ABERTO,
        })
        .expect(201)
        .expect((res) => {
          expect(res.body).toHaveProperty('id');
          expect(res.body.title).toBe('Tarefa de Teste E2E');
          expect(res.body.description).toBe('Descrição da tarefa de teste');
          expect(res.body.status).toBe(TaskStatus.ABERTO);
          createdTaskId = res.body.id; // Salvar ID para próximos testes
        });
    });

    it('deve retornar 400 para dados inválidos', () => {
      return request(app.getHttpServer())
        .post('/tasks')
        .send({
          title: '', // título vazio - inválido
          description: 'Descrição',
        })
        .expect(400);
    });

    it('deve retornar 400 para campos não permitidos', () => {
      return request(app.getHttpServer())
        .post('/tasks')
        .send({
          title: 'Tarefa',
          description: 'Descrição',
          campoInvalido: 'valor',
        })
        .expect(400);
    });
  });

  describe('/tasks (GET)', () => {
    it('deve retornar array de tarefas', () => {
      return request(app.getHttpServer())
        .get('/tasks')
        .expect(200)
        .expect((res) => {
          expect(Array.isArray(res.body)).toBe(true);
          expect(res.body.length).toBeGreaterThan(0);
        });
    });
  });

  describe('/tasks/:id (GET)', () => {
    it('deve retornar uma tarefa específica', () => {
      return request(app.getHttpServer())
        .get(`/tasks/${createdTaskId}`)
        .expect(200)
        .expect((res) => {
          expect(res.body).toHaveProperty('id', createdTaskId);
          expect(res.body).toHaveProperty('title');
          expect(res.body).toHaveProperty('description');
          expect(res.body).toHaveProperty('status');
        });
    });

    it('deve retornar 404 para tarefa inexistente', () => {
      return request(app.getHttpServer())
        .get('/tasks/99999')
        .expect(404);
    });

    it('deve retornar 400 para ID inválido', () => {
      return request(app.getHttpServer())
        .get('/tasks/abc')
        .expect(400);
    });
  });

  describe('/tasks/:id (PUT)', () => {
    it('deve atualizar uma tarefa existente', () => {
      return request(app.getHttpServer())
        .put(`/tasks/${createdTaskId}`)
        .send({
          title: 'Tarefa Atualizada',
          description: 'Descrição atualizada',
          status: TaskStatus.FAZENDO,
        })
        .expect(200)
        .expect((res) => {
          expect(res.body.id).toBe(createdTaskId);
          expect(res.body.title).toBe('Tarefa Atualizada');
          expect(res.body.description).toBe('Descrição atualizada');
          expect(res.body.status).toBe(TaskStatus.FAZENDO);
        });
    });

    it('deve atualizar parcialmente uma tarefa', () => {
      return request(app.getHttpServer())
        .put(`/tasks/${createdTaskId}`)
        .send({
          status: TaskStatus.FINALIZADO,
        })
        .expect(200)
        .expect((res) => {
          expect(res.body.status).toBe(TaskStatus.FINALIZADO);
        });
    });

    it('deve retornar 404 para tarefa inexistente', () => {
      return request(app.getHttpServer())
        .put('/tasks/99999')
        .send({
          title: 'Título',
        })
        .expect(404);
    });
  });

  describe('/tasks/:id (DELETE)', () => {
    it('deve deletar uma tarefa existente', () => {
      return request(app.getHttpServer())
        .delete(`/tasks/${createdTaskId}`)
        .expect(204);
    });

    it('deve retornar 404 ao tentar deletar tarefa já deletada', () => {
      return request(app.getHttpServer())
        .delete(`/tasks/${createdTaskId}`)
        .expect(404);
    });
  });
});
```

### 3.4 Métodos HTTP do Supertest

```typescript
// GET
request(app.getHttpServer())
  .get('/rota')
  .expect(200);

// POST
request(app.getHttpServer())
  .post('/rota')
  .send({ data: 'valor' })
  .expect(201);

// PUT
request(app.getHttpServer())
  .put('/rota/1')
  .send({ data: 'novo valor' })
  .expect(200);

// PATCH
request(app.getHttpServer())
  .patch('/rota/1')
  .send({ campo: 'valor' })
  .expect(200);

// DELETE
request(app.getHttpServer())
  .delete('/rota/1')
  .expect(204);

// Adicionar Headers
request(app.getHttpServer())
  .get('/rota')
  .set('Authorization', 'Bearer token')
  .expect(200);

// Verificar Headers de resposta
request(app.getHttpServer())
  .get('/rota')
  .expect('Content-Type', /json/)
  .expect(200);

// Verificar corpo da resposta
request(app.getHttpServer())
  .get('/rota')
  .expect(200)
  .expect((res) => {
    expect(res.body.campo).toBe('valor');
  });
```

### 3.5 Configuração de Banco de Dados para E2E

Para testes E2E, é recomendado usar um banco de dados separado:

```typescript
// test/jest-e2e.json
{
  "moduleFileExtensions": ["js", "json", "ts"],
  "rootDir": ".",
  "testEnvironment": "node",
  "testRegex": ".e2e-spec.ts$",
  "transform": {
    "^.+\\.(t|j)s$": "ts-jest"
  }
}
```

**Dica**: Para SQLite (usado neste projeto), o banco é criado automaticamente. Para outros bancos, configure variáveis de ambiente específicas para teste.

### 3.6 Boas Práticas para Testes E2E

1. **Use beforeAll/afterAll**: Crie a aplicação uma vez para todos os testes
2. **Limpe dados entre testes**: Garanta que cada teste inicia com estado limpo
3. **Teste fluxos completos**: Simule ações reais de usuários
4. **Ordem dos testes**: Use dados criados em testes anteriores quando apropriado
5. **Verifique status HTTP**: Sempre valide o código de resposta esperado
6. **Teste casos de erro**: Valide respostas para requisições inválidas
7. **Aplique mesmas configurações**: Use ValidationPipe e outras configs do main.ts

### 3.7 Diferenças entre Testes Unitários e E2E

| Aspecto | Testes Unitários (*.spec.ts) | Testes E2E (*.e2e-spec.ts) |
|---------|------------------------------|----------------------------|
| **Escopo** | Componente isolado | Aplicação completa |
| **Velocidade** | Muito rápidos | Mais lentos |
| **Banco de Dados** | Mock/Fake | Real (ou teste) |
| **HTTP** | Não faz requisições | Requisições reais |
| **Dependências** | Todas mockadas | Reais |
| **Quantidade** | Muitos testes | Menos testes |
| **Objetivo** | Lógica de negócio | Integração completa |

---

## Conclusão

Testes automatizados são essenciais para desenvolvimento de software de qualidade. No NestJS:

- **Testes Unitários (*.spec.ts)**: Validam lógica de componentes isolados
- **Testes E2E (*.e2e-spec.ts)**: Validam funcionamento completo da API

### Comandos Principais

```bash
# Instalar dependências
npm install

# Testes unitários
npm test
npm run test:watch
npm run test:cov

# Testes E2E
npm run test:e2e

# Executar todos os testes
npm test && npm run test:e2e
```

### Recursos Adicionais

- [Documentação NestJS - Testing](https://docs.nestjs.com/fundamentals/testing)
- [Jest Documentation](https://jestjs.io/)
- [Supertest GitHub](https://github.com/visionmedia/supertest)

---

## Estrutura do Projeto

```
src/
├── app.controller.ts          # Controller principal
├── app.controller.spec.ts     # Testes unitários do controller
├── app.service.ts             # Service principal
├── app.module.ts              # Módulo raiz
├── main.ts                    # Ponto de entrada
└── tasks/
    ├── tasks.controller.ts    # Controller de tarefas
    ├── tasks.service.ts       # Service de tarefas
    ├── tasks.module.ts        # Módulo de tarefas
    ├── task.entity.ts         # Entidade Task
    └── dto/
        ├── create-task.dto.ts # DTO para criar tarefa
        └── update-task.dto.ts # DTO para atualizar tarefa

test/
├── app.e2e-spec.ts           # Testes E2E do app
└── jest-e2e.json             # Configuração Jest E2E
```

### Rotas da API

| Método | Rota | Descrição |
|--------|------|-----------|
| GET | / | Informações da API |
| GET | /tasks | Listar todas as tarefas |
| GET | /tasks/:id | Buscar tarefa por ID |
| POST | /tasks | Criar nova tarefa |
| PUT | /tasks/:id | Atualizar tarefa |
| DELETE | /tasks/:id | Deletar tarefa |

---

**Desenvolvido para a turma de Infoweb 2025**

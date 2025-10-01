# Tutorial de Testes Automatizados em NestJS

Este tutorial apresenta conceitos e práticas de testes automatizados utilizando o framework NestJS, com exemplos baseados em uma API de gerenciamento de tarefas (ToDo).

---

## 📑 Sumário

- [Tutorial de Testes Automatizados em NestJS](#tutorial-de-testes-automatizados-em-nestjs)
  - [📑 Sumário](#-sumário)
  - [Parte 1: Introdução Teórica sobre Testes Automatizados](#parte-1-introdução-teórica-sobre-testes-automatizados)
    - [1.1 O que são Testes Automatizados?](#11-o-que-são-testes-automatizados)
    - [1.2 Tipos de Testes](#12-tipos-de-testes)
      - [1.2.1 Testes Unitários (Unit Tests)](#121-testes-unitários-unit-tests)
      - [1.2.2 Testes de Integração (Integration Tests)](#122-testes-de-integração-integration-tests)
      - [1.2.3 Testes End-to-End (E2E Tests)](#123-testes-end-to-end-e2e-tests)
    - [1.3 Pirâmide de Testes](#13-pirâmide-de-testes)
    - [1.4 Ferramentas no NestJS](#14-ferramentas-no-nestjs)
    - [1.5 Padrão AAA (Arrange-Act-Assert)](#15-padrão-aaa-arrange-act-assert)
    - [1.6 Comandos de Teste](#16-comandos-de-teste)
  - [Parte 2: Testes Unitários (\*.spec.ts)](#parte-2-testes-unitários-spects)
    - [2.1 Estrutura Básica de um Teste Unitário](#21-estrutura-básica-de-um-teste-unitário)
    - [2.2 Exemplo Real: Testando o AppController](#22-exemplo-real-testando-o-appcontroller)
    - [2.3 Testando Services com Dependências](#23-testando-services-com-dependências)
    - [2.4 Principais Matchers do Jest](#24-principais-matchers-do-jest)
    - [2.5 Boas Práticas para Testes Unitários](#25-boas-práticas-para-testes-unitários)
  - [Parte 3: Testes E2E (\*.e2e-spec.ts)](#parte-3-testes-e2e-e2e-spects)
    - [3.1 Estrutura Básica de um Teste E2E](#31-estrutura-básica-de-um-teste-e2e)
    - [3.2 Exemplo Real: Testando a Rota Principal](#32-exemplo-real-testando-a-rota-principal)
    - [3.3 Testando API de Tarefas (CRUD Completo)](#33-testando-api-de-tarefas-crud-completo)
    - [3.4 Métodos HTTP do Supertest](#34-métodos-http-do-supertest)
    - [3.5 Configuração de Banco de Dados para E2E](#35-configuração-de-banco-de-dados-para-e2e)
      - [Configuração para PostgreSQL](#configuração-para-postgresql)
    - [3.6 Boas Práticas para Testes E2E](#36-boas-práticas-para-testes-e2e)
    - [3.7 Diferenças entre Testes Unitários e E2E](#37-diferenças-entre-testes-unitários-e-e2e)
  - [Conclusão](#conclusão)
    - [Comandos Principais](#comandos-principais)
    - [Recursos Adicionais](#recursos-adicionais)
  - [Estrutura do Projeto](#estrutura-do-projeto)
    - [Rotas da API](#rotas-da-api)

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

1. no terminal, execute `npm run test:watch`. deverá aparecer um menu, conforme **saída 1**.
2. selecione `a` para executar todos os testes. deverá aparecer um _fail_, conforme saída abaixo **saída 2**.
3. modifique o arquivo `src/app.controller.spec.ts`, salve. será mostrado algo como a **saída 3** informando que os testes foram "passados".
4. adicione os testes para a rota `/tasks`

**saída 1**
```bash

No tests found related to files changed since last commit.
Press `a` to run all tests, or run Jest with `--watchAll`.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.

```

**saída 2**
```bash

 FAIL  src/app.controller.spec.ts
  AppController
    root
      ✕ should return "Hello World!" (18 ms)

  ● AppController › root › should return "Hello World!"

    TypeError: appController.getHello is not a function

      17 |   describe('root', () => {
      18 |     it('should return "Hello World!"', () => {
    > 19 |       expect(appController.getHello()).toBe('Hello World!');
         |                            ^
      20 |     });
      21 |   });
      22 | });

      at Object.<anonymous> (app.controller.spec.ts:19:28)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        1.251 s, estimated 2 s
Ran all test suites.

```

**saída 3**
```bash

 PASS  src/app.controller.spec.ts
  AppController
    getInfo
      ✓ deve retornar informações da API com status, version e description (53 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        3.871 s
Ran all test suites.

```

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

Quando um serviço possui dependências (como repositórios do TypeORM), precisamos usar **mocks**, conforme código abaixo.

1. ainda com o terminal com o teste em execução, adicione o arquivo `/src/tasks/tasks.controller.spec.ts`. o teste irá informar que falhou, conforme **saída 4**.
2. modifique o arquivo `src/tasks/tasks.controller.spec.ts`, conforme código abaixo e salve. o teste passará conforme **saída 5**.

Importante, para cada arquivo `*.controller.ts` com _endpoints_, precisa de um arquivo de teste correspondente `*.controller.spec.ts` com seus testes automatizados.
Na **saída 5** é mostrado a execução de 2 arquivos `src/app.controller.spec.ts` e `src/tasks/tasks.controller.spec.ts`.

**saída 4**
```bash

 FAIL  src/tasks/tasks.controller.spec.ts
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at onResult (../node_modules/@jest/core/build/index.js:1057:18)
      at ../node_modules/@jest/core/build/index.js:1127:165
      at ../node_modules/emittery/index.js:363:13
          at Array.map (<anonymous>)
      at Emittery.emit (../node_modules/emittery/index.js:361:23)

 PASS  src/app.controller.spec.ts

Test Suites: 1 failed, 1 passed, 2 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.435 s
Ran all test suites.

Watch Usage: Press w to show more.

```

**saída 4**
```bash

 PASS  src/app.controller.spec.ts
 PASS  src/tasks/tasks.controller.spec.ts

Test Suites: 2 passed, 2 total
Tests:       5 passed, 5 total
Snapshots:   0 total
Time:        2.995 s
Ran all test suites.

Watch Usage: Press w to show more.

```

**Arquivo**: `src/tasks/tasks.controller.spec.ts`

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

// Objetos
expect(object).toHaveProperty('status');                    // Verifica se propriedade existe
expect(object).toHaveProperty('status', 'online');          // Verifica propriedade e valor
expect(object).toHaveProperty('user.name');                 // Propriedade aninhada
expect(object).toHaveProperty('config.timeout', 3000);      // Propriedade aninhada com valor
expect(object).toMatchObject({ status: 'online' });         // Verifica subset de propriedades
expect(object).toEqual({ status: 'online', version: '1.0' }); // Igualdade exata de objeto

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

1. se ainda não finalizou os testes unitários, pressine a tecla `q` para encerrar os testes.
2. no arquivo `package.json`, adicione a linha `"test:e2e:watch": "jest --watch --config ./test/jest-e2e.json",` antes da linha `"test:e2e": "jest --config ./test/jest-e2e.json"`. isso irá criar o _script_ `test:e2e:watch`.
3. execute os testes e2e com o comando `npm run test:e2e:watch`. irá aparecer erro, conforme **saída 6**.
4. modifique o arquivo `test/app.e2e-spec.ts` conforme código abaixo. irá aparecer o resultado da **saída 7**.


**saída 6**
```bash

 FAIL  test/app.e2e-spec.ts
  AppController (e2e)
    ✕ / (GET) (345 ms)

  ● AppController (e2e) › / (GET)

    expected 'Hello World!' response body, got '{"status":"online","version":"1.0.0","description":"Esta é API de tarefas (todos) da turma de Infoweb 2025."}'

      21 |       .get('/')
      22 |       .expect(200)
    > 23 |       .expect('Hello World!');
         |        ^
      24 |   });
      25 | });
      26 |

      at Object.<anonymous> (app.e2e-spec.ts:23:8)
      ----
      at error (../node_modules/supertest/lib/test.js:392:15)
      at Test._assertBody (../node_modules/supertest/lib/test.js:263:16)
      at ../node_modules/supertest/lib/test.js:365:13
      at Test._assertFunction (../node_modules/supertest/lib/test.js:342:13)
      at Test.assert (../node_modules/supertest/lib/test.js:195:23)
      at localAssert (../node_modules/supertest/lib/test.js:138:14)
      at Server.<anonymous> (../node_modules/supertest/lib/test.js:152:11)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        2.259 s
Ran all test suites related to changed files.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press i to run failing tests interactively.
 › Press Enter to trigger a test run.

```


**saída 7**
```bash

 PASS  test/app.e2e-spec.ts
  AppController (e2e)
    ✓ / (GET) - deve retornar informações da API (309 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        3.048 s
Ran all test suites related to changed files.

Watch Usage: Press w to show more.

```

**Arquivo**: `test/app.e2e-spec.ts`
```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import request from 'supertest';
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

1. ainda com o monitoramento do teste, adicione o arquivo `test/tasks.e2e-spec.ts`. o resultado esta na **saída 8**.
2. modifique o arquivo `test/tasks.e2e-spec.ts` conforme código abaixo. um resultado esperado é mostrado na **saída 9**.


**saída 8**
```bash

 FAIL  test/tasks.e2e-spec.ts
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at onResult (../node_modules/@jest/core/build/index.js:1057:18)
      at ../node_modules/@jest/core/build/index.js:1127:165
      at ../node_modules/emittery/index.js:363:13
          at Array.map (<anonymous>)
      at Emittery.emit (../node_modules/emittery/index.js:361:23)

 PASS  test/app.e2e-spec.ts

Test Suites: 1 failed, 1 passed, 2 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        4.357 s
Ran all test suites related to changed files.

Watch Usage: Press w to show more.

```

**saída 8**
```bash

[Nest] 137590  - 01/10/2025, 10:32:08   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
QueryFailedError: SQLITE_BUSY: database is locked
    at Statement.handler (/home/minora/minora/nest/api-nest-notas-02-testes/src/driver/sqlite/SqliteQueryRunner.ts:133:29)
    at Statement.replacement (/home/minora/minora/nest/api-nest-notas-02-testes/node_modules/sqlite3/lib/trace.js:25:27)
    at Statement.replacement (/home/minora/minora/nest/api-nest-notas-02-testes/node_modules/sqlite3/lib/trace.js:25:27)
 PASS  test/tasks.e2e-spec.ts
 PASS  test/app.e2e-spec.ts (5.032 s)

Test Suites: 2 passed, 2 total
Tests:       13 passed, 13 total
Snapshots:   0 total
Time:        5.669 s, estimated 7 s
Ran all test suites related to changed files.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.

```


**Arquivo**: `test/tasks.e2e-spec.ts` (exemplo completo)

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication, ValidationPipe } from '@nestjs/common';
import request from 'supertest';
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

**Dica**: Para SQLite (usado neste projeto), o banco é criado automaticamente.
Para outros bancos, configure variáveis de ambiente específicas para teste.

#### Configuração para PostgreSQL

Para usar PostgreSQL em testes E2E, siga estas práticas:

**1. Criar arquivo de configuração de teste**

Crie um arquivo `.env.test` na raiz do projeto:

```env
# .env.test
DATABASE_HOST=localhost
DATABASE_PORT=5433
DATABASE_USER=postgres
DATABASE_PASSWORD=postgres
DATABASE_NAME=test_database
NODE_ENV=test
```

**2. Configurar TypeORM para testes**

Modifique o módulo de teste para usar banco de dados de teste:

```typescript
// test/app.e2e-spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import * as request from 'supertest';

describe('AppController (e2e)', () => {
  let app: INestApplication;

  beforeAll(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [
        TypeOrmModule.forRoot({
          type: 'postgres',
          host: process.env.DATABASE_HOST || 'localhost',
          port: parseInt(process.env.DATABASE_PORT || '5432'),
          username: process.env.DATABASE_USER || 'postgres',
          password: process.env.DATABASE_PASSWORD || 'postgres',
          database: process.env.DATABASE_NAME || 'test_database',
          entities: [__dirname + '/../src/**/*.entity{.ts,.js}'],
          synchronize: true, // Apenas para testes
          dropSchema: true,  // Limpa banco antes de cada execução
        }),
        // ... outros módulos
      ],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  // ... seus testes
});
```

**3. Script para executar testes com PostgreSQL**

Adicione no `package.json`:

```json
{
  "scripts": {
    "test:e2e": "NODE_ENV=test jest --config ./test/jest-e2e.json",
    "test:e2e:watch": "NODE_ENV=test jest --config ./test/jest-e2e.json --watch"
  }
}
```

**4. Usar Docker para banco de teste**

Crie um `docker-compose.test.yml`:

```yaml
services:
  postgres_test:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: test_database
    ports:
      - "5433:5432"
    tmpfs:
      - /var/lib/postgresql/data  # Banco em memória para testes mais rápidos
```

Execute os testes com:

```bash
# Iniciar banco de teste
docker-compose -f docker-compose.test.yml up -d

# Executar testes
npm run test:e2e

# Parar banco de teste
docker-compose -f docker-compose.test.yml down
```

**Dicas importantes para PostgreSQL:**

- ✅ Use `dropSchema: true` em testes para garantir estado limpo
- ✅ Use `synchronize: true` apenas em ambiente de teste
- ✅ Configure timeout maior para testes com banco de dados
- ✅ Use transações para isolar testes (opcional)
- ✅ Considere usar banco em memória (tmpfs no Docker) para testes mais rápidos
- ❌ Nunca use banco de produção para testes
- ❌ Não compartilhe banco entre testes unitários e E2E

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

**Desenvolvido by github copilot agent para a turma de Infoweb 2025**

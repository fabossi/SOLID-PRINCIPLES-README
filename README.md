# Princípios SOLID

Os princípios SOLID são um conjunto de boas práticas de design de software que tornam os sistemas mais compreensíveis, flexíveis e fáceis de manter. A sigla SOLID refere-se a cinco princípios:

- **S**: Single Responsibility Principle (SRP)
- **O**: Open/Closed Principle (OCP)
- **L**: Liskov Substitution Principle (LSP)
- **I**: Interface Segregation Principle (ISP)
- **D**: Dependency Inversion Principle (DIP)

## 1. Single Responsibility Principle (SRP)

Cada classe deve ter uma única responsabilidade. Isso significa que uma classe deve ter apenas uma razão para mudar.

**Exemplo:**

```typescript
// Violando o SRP: Uma classe que faz muitas coisas.
class User {
  constructor(public name: string, public email: string) {}

  validateEmail() {
    // Lógica de validação de email
  }

  saveUser() {
    // Lógica de salvar o usuário
  }

  sendEmail() {
    // Lógica para enviar email
  }
}

// Aplicando o SRP: Cada classe tem uma responsabilidade única.
class User {
  constructor(public name: string, public email: string) {}
}

class EmailValidator {
  validate(email: string) {
    // Lógica de validação de email
  }
}

class UserRepository {
  save(user: User) {
    // Lógica para salvar o usuário
  }
}

class EmailService {
  send(email: string) {
    // Lógica para enviar email
  }
}
```

## 2. Open/Closed Principle (OCP)

Uma classe deve estar aberta para extensão, mas fechada para modificação. Isso significa que você deve ser capaz de adicionar novos comportamentos sem alterar o código existente.

**Exemplo:**

```typescript
// Violando o OCP: Modificando a classe para cada novo tipo de desconto.
class Discount {
  calculate(price: number, discountType: string): number {
    if (discountType === 'seasonal') {
      return price * 0.9;
    } else if (discountType === 'clearance') {
      return price * 0.5;
    }
    return price;
  }
}

// Aplicando o OCP: Adicionando novos descontos sem modificar a classe base.
interface DiscountStrategy {
  calculate(price: number): number;
}

class SeasonalDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.9;
  }
}

class ClearanceDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.5;
  }
}

class Discount {
  apply(price: number, discount: DiscountStrategy): number {
    return discount.calculate(price);
  }
}
```

## 3. Liskov Substitution Principle (LSP)

Objetos de uma superclasse devem poder ser substituídos por objetos de suas subclasses sem que o comportamento correto do programa seja alterado. Ou seja, uma subclasse deve poder substituir a superclasse sem quebrar o código.

**Exemplo:**

```typescript
// Violando o LSP: A subclasse altera o comportamento esperado.
class Bird {
  fly() {
    console.log("Flying...");
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can't fly!");
  }
}

// Aplicando o LSP: As classes respeitam o comportamento esperado.
class Bird {
  move() {
    console.log("Moving...");
  }
}

class FlyingBird extends Bird {
  fly() {
    console.log("Flying...");
  }
}

class Penguin extends Bird {
  swim() {
    console.log("Swimming...");
  }
}
```

## 4. Interface Segregation Principle (ISP)

Os clientes não devem ser forçados a depender de interfaces que não utilizam. Em vez de usar interfaces grandes, crie várias interfaces pequenas e específicas.

**Exemplo:**

```typescript
// Violando o ISP: Forçando classes a implementar métodos que não precisam.
interface Worker {
  work(): void;
  eat(): void;
}

class HumanWorker implements Worker {
  work() {
    console.log("Working...");
  }

  eat() {
    console.log("Eating...");
  }
}

class RobotWorker implements Worker {
  work() {
    console.log("Working...");
  }

  eat() {
    throw new Error("Robots don't eat!");
  }
}

// Aplicando o ISP: Interfaces segregadas com responsabilidades específicas.
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

class HumanWorker implements Workable, Eatable {
  work() {
    console.log("Working...");
  }

  eat() {
    console.log("Eating...");
  }
}

class RobotWorker implements Workable {
  work() {
    console.log("Working...");
  }
}
```

# 📚 Guia Completo: Princípios SOLID e GRASP em JavaScript

## 🎯 Introdução

Este guia demonstra a implementação dos princípios SOLID e GRASP usando JavaScript moderno, com exemplos práticos e explicações detalhadas.

## 📌 SOLID em JavaScript

### 1. S - Single Responsibility Principle (Princípio da Responsabilidade Única)

```javascript
// ❌ RUIM: Classe com múltiplas responsabilidades
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }

    // Responsabilidade 1: Validação de dados
    validateEmail() {
        return this.email.includes('@');
    }

    // Responsabilidade 2: Persistência de dados
    saveToDatabase() {
        // Código para salvar no banco de dados
        console.log(`Salvando usuário ${this.name} no banco de dados`);
    }

    // Responsabilidade 3: Envio de email
    sendWelcomeEmail() {
        // Código para enviar email
        console.log(`Enviando email de boas-vindas para ${this.email}`);
    }
}

// ✅ BOM: Separando responsabilidades
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
}

class UserValidator {
    static validateEmail(email) {
        return email.includes('@');
    }
}

class UserRepository {
    saveUser(user) {
        // Código para salvar no banco de dados
        console.log(`Salvando usuário ${user.name} no banco de dados`);
    }
}

class EmailService {
    sendWelcomeEmail(user) {
        // Código para enviar email
        console.log(`Enviando email de boas-vindas para ${user.email}`);
    }
}

// Uso:
const user = new User('João', 'joao@email.com');
const validator = new UserValidator();
const repository = new UserRepository();
const emailService = new EmailService();

if (UserValidator.validateEmail(user.email)) {
    repository.saveUser(user);
    emailService.sendWelcomeEmail(user);
}
```

### 2. O - Open/Closed Principle (Princípio Aberto/Fechado)

```javascript
// ❌ RUIM: Violando Open/Closed
class DiscountCalculator {
    calculateDiscount(order, customerType) {
        if (customerType === 'regular') {
            return order.total * 0.1;
        } else if (customerType === 'vip') {
            return order.total * 0.2;
        } else if (customerType === 'premium') {  // Se precisar adicionar novo tipo, precisa modificar a classe
            return order.total * 0.3;
        }
    }
}

// ✅ BOM: Usando Open/Closed
class Discount {
    calculate(order) {
        throw new Error('Método calculate deve ser implementado');
    }
}

class RegularDiscount extends Discount {
    calculate(order) {
        return order.total * 0.1;
    }
}

class VIPDiscount extends Discount {
    calculate(order) {
        return order.total * 0.2;
    }
}

class PremiumDiscount extends Discount {
    calculate(order) {
        return order.total * 0.3;
    }
}

class DiscountCalculator {
    constructor(discountStrategy) {
        this.discountStrategy = discountStrategy;
    }

    calculateDiscount(order) {
        return this.discountStrategy.calculate(order);
    }
}

// Uso:
const order = { total: 100 };
const regularCalculator = new DiscountCalculator(new RegularDiscount());
const vipCalculator = new DiscountCalculator(new VIPDiscount());

console.log(regularCalculator.calculateDiscount(order)); // 10
console.log(vipCalculator.calculateDiscount(order));    // 20
```

### 3. L - Liskov Substitution Principle (Princípio da Substituição de Liskov)

```javascript
// ❌ RUIM: Violando Liskov
class Bird {
    fly() {
        return "Voando...";
    }
}

class Penguin extends Bird {
    fly() {
        throw new Error("Não posso voar!"); // Viola Liskov!
    }
}

// ✅ BOM: Respeitando Liskov
class Animal {
    move() {
        throw new Error('Método move deve ser implementado');
    }
}

class FlyingBird extends Animal {
    move() {
        return "Voando...";
    }
}

class WalkingBird extends Animal {
    move() {
        return "Andando...";
    }
}

class SwimmingBird extends Animal {
    move() {
        return "Nadando...";
    }
}

// Uso:
function moveAnimal(animal) {
    return animal.move();
}

const eagle = new FlyingBird();
const penguin = new WalkingBird();
const duck = new SwimmingBird();

console.log(moveAnimal(eagle));   // Voando...
console.log(moveAnimal(penguin)); // Andando...
console.log(moveAnimal(duck));    // Nadando...
```

### 4. I - Interface Segregation Principle (Princípio da Segregação de Interface)

```javascript
// ❌ RUIM: Interface muito grande
class Printer {
    print() { throw new Error('Método não implementado'); }
    scan() { throw new Error('Método não implementado'); }
    fax() { throw new Error('Método não implementado'); }
    copy() { throw new Error('Método não implementado'); }
}

class SimplePrinter extends Printer {
    print() {
        console.log('Imprimindo...');
    }
    
    // Precisa implementar métodos que não usa
    scan() { throw new Error('Scanner não suportado'); }
    fax() { throw new Error('Fax não suportado'); }
    copy() { throw new Error('Cópia não suportada'); }
}

// ✅ BOM: Interfaces segregadas
class Printable {
    print() { throw new Error('Método não implementado'); }
}

class Scannable {
    scan() { throw new Error('Método não implementado'); }
}

class Faxable {
    fax() { throw new Error('Método não implementado'); }
}

class Copyable {
    copy() { throw new Error('Método não implementado'); }
}

// Agora cada dispositivo implementa apenas o que precisa
class SimplePrinter extends Printable {
    print() {
        console.log('Imprimindo...');
    }
}

class AllInOnePrinter extends Printable {
    print() {
        console.log('Imprimindo...');
    }
    
    scan() {
        console.log('Escaneando...');
    }
    
    copy() {
        console.log('Copiando...');
    }
}

// Uso:
const simplePrinter = new SimplePrinter();
simplePrinter.print(); // Funciona
```

### 5. D - Dependency Inversion Principle (Princípio da Inversão de Dependência)

```javascript
// ❌ RUIM: Dependência direta de implementações
class MySQLDatabase {
    save(data) {
        console.log('Salvando no MySQL:', data);
    }
}

class UserService {
    constructor() {
        this.database = new MySQLDatabase(); // Dependência direta!
    }

    saveUser(user) {
        this.database.save(user);
    }
}

// ✅ BOM: Usando injeção de dependência
class Database {
    save(data) {
        throw new Error('Método save deve ser implementado');
    }
}

class MySQLDatabase extends Database {
    save(data) {
        console.log('Salvando no MySQL:', data);
    }
}

class MongoDatabase extends Database {
    save(data) {
        console.log('Salvando no MongoDB:', data);
    }
}

class UserService {
    constructor(database) {
        this.database = database;
    }

    saveUser(user) {
        this.database.save(user);
    }
}

// Uso:
const mysqlService = new UserService(new MySQLDatabase());
const mongoService = new UserService(new MongoDatabase());

mysqlService.saveUser({name: 'João'});
mongoService.saveUser({name: 'Maria'});
```

## 📌 GRASP (General Responsibility Assignment Software Patterns)

### 1. Creator (Criador)

```javascript
// ✅ BOM: Aplicando o padrão Creator
class Product {
    constructor(name, price) {
        this.name = name;
        this.price = price;
    }
}

class OrderItem {
    constructor(product, quantity) {
        this.product = product;
        this.quantity = quantity;
        this.subtotal = this.calculateSubtotal();
    }

    calculateSubtotal() {
        return this.product.price * this.quantity;
    }
}

class Order {
    constructor() {
        this.items = [];
        this.total = 0;
    }

    // Order é responsável por criar OrderItem porque:
    // 1. Order contém OrderItem
    // 2. Order tem as informações necessárias
    addItem(product, quantity) {
        const item = new OrderItem(product, quantity);
        this.items.push(item);
        this.calculateTotal();
        return item;
    }

    calculateTotal() {
        this.total = this.items.reduce((sum, item) => sum + item.subtotal, 0);
    }
}

// Uso:
const product = new Product('Notebook', 3000);
const order = new Order();
order.addItem(product, 2);
console.log(order.total); // 6000
```

### 2. Information Expert (Especialista na Informação)

```javascript
// ✅ BOM: Aplicando Information Expert
class CartItem {
    constructor(product, quantity) {
        this.product = product;
        this.quantity = quantity;
    }

    // CartItem é expert em calcular seu próprio subtotal
    getSubtotal() {
        return this.product.price * this.quantity;
    }
}

class ShoppingCart {
    constructor() {
        this.items = [];
    }

    addItem(product, quantity) {
        const item = new CartItem(product, quantity);
        this.items.push(item);
    }

    // ShoppingCart é expert em calcular o total
    getTotal() {
        return this.items.reduce((total, item) => total + item.getSubtotal(), 0);
    }

    // ShoppingCart é expert em verificar se está vazio
    isEmpty() {
        return this.items.length === 0;
    }
}

// Uso:
const cart = new ShoppingCart();
cart.addItem(new Product('Mouse', 50), 2);
cart.addItem(new Product('Teclado', 100), 1);

console.log(cart.getTotal()); // 200
console.log(cart.isEmpty()); // false
```

### 3. Low Coupling (Baixo Acoplamento)

```javascript
// ❌ RUIM: Alto acoplamento
class OrderProcessor {
    constructor() {
        this.emailService = new EmailService();
        this.inventoryService = new InventoryService();
        this.paymentService = new PaymentService();
    }

    processOrder(order) {
        this.paymentService.processPayment(order);
        this.inventoryService.updateStock(order);
        this.emailService.sendConfirmation(order);
    }
}

// ✅ BOM: Baixo acoplamento usando injeção de dependência
class OrderProcessor {
    constructor(paymentService, inventoryService, emailService) {
        this.paymentService = paymentService;
        this.inventoryService = inventoryService;
        this.emailService = emailService;
    }

    processOrder(order) {
        this.paymentService.processPayment(order);
        this.inventoryService.updateStock(order);
        this.emailService.sendConfirmation(order);
    }
}

// Uso:
const processor = new OrderProcessor(
    new PaymentService(),
    new InventoryService(),
    new EmailService()
);
```

## 📚 Conclusão

Seguir estes princípios ajuda a criar código:
- Mais organizado
- Fácil de manter
- Mais testável
- Mais flexível
- Mais reutilizável

Lembre-se: Não é necessário aplicar todos os princípios em todas as situações. Use-os como guias para melhorar seu código quando fizer sentido.

---

Esses são os cinco princípios do SOLID, com exemplos de como eles podem ser aplicados na prática para tornar o código mais robusto, escalável e fácil de manter.

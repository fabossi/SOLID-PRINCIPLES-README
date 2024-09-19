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

## 5. Dependency Inversion Principle (DIP)

Dependa de abstrações, não de implementações. Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.

**Exemplo:**

```typescript
// Violando o DIP: A classe depende de uma implementação concreta.
class LightBulb {
  turnOn() {
    console.log("Light bulb is on");
  }

  turnOff() {
    console.log("Light bulb is off");
  }
}

class Switch {
  private lightBulb: LightBulb;

  constructor(lightBulb: LightBulb) {
    this.lightBulb = lightBulb;
  }

  toggle() {
    this.lightBulb.turnOn();
  }
}

// Aplicando o DIP: Dependendo de uma abstração (interface).
interface Switchable {
  turnOn(): void;
  turnOff(): void;
}

class LightBulb implements Switchable {
  turnOn() {
    console.log("Light bulb is on");
  }

  turnOff() {
    console.log("Light bulb is off");
  }
}

class Switch {
  private device: Switchable;

  constructor(device: Switchable) {
    this.device = device;
  }

  toggle() {
    this.device.turnOn();
  }
}
```

---

Esses são os cinco princípios do SOLID, com exemplos de como eles podem ser aplicados na prática para tornar o código mais robusto, escalável e fácil de manter.

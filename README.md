# **SOLID**

> Os príncipios SOLID nos dizem como organizar as funções e estruturas de dados em classes e como essas classes devem ser interconectadas.

A palavra *classe* não implica em que esses principios sejam aplicáveis apenas ao paradigma de orientação a objetos. *classe* é apenas um agrupamento acoplado de funções e dados.

## **Objetivo**

O objetivo dos princípios é a criação de estruturas de software de nivel médio que:

- Tolerem mudanças;
- Sejam fáceis de entender;
- Sejam a base de componentes que possam ser usados em muitos sistemas de software;



O termo "nível médio" se refere ao fato de que esses princípios são aplicados por programadores que trabalham no nível do módulo. Sua aplicação ocorre logo acima do nível de código e visa definir os tipos de estruturas de software usadas dentro de módulos e componentes.

## **SRP: Single Responsibility Principle**

<img src="https://miro.medium.com/max/700/1*2lOJXH438qRn_KJpwzxTFw.png"/>

De todos os princípios, o SRP provavelmente é o menos compreendido. 

Históricamente, o SRP tem sido descrito como:

> Um módulo deve ter uma, e apenas uma, razão para mudar.

Mas sua definição final é:

> Um módulo deve ser responsável por um, e apenas um, ator.

Um exemplo clássico de violação do SRP:

```typescript
// Exemplo de aplicação de folha de pagamento

class Employee {
  public calculatePay (): number {
    // implementa algoritimo para RH, Contabilidade e TI
    // if (isRh) {
    // }
    // else if (isAccounting) {
    // } else {
    // }
  }
  public reportHours (): number {
    // implementa algoritimo para RH, Contabilidade e TI
  }

  public save (): Promise<any> {
    // implementa algoritimo para RH, Contabilidade e TI
  }
}
```

A classe `Employee` viola o SRP porque esses 3 métodos são responsáveis por 3 atores diferentes: RH, Contabilidade e TI.

```typescript
abstract class Employee {
  abstract calculatePay (): number;
  abstract reportHours (): number;

  protected save (): Promise<any> {
    // Algoritimo em comum
  }
}

class HR extends Employee {
  calculatePay (): number {
    // Algoritmo especifio para RH
  }
  reportHours (): number {
    // Algoritmo especifio para RH
  }
}

class Accounting extends Employee {
  calculatePay (): number {
    // Algoritmo especifio para Contabilidade
  }
  reportHours (): number {
    // Algoritmo especifio para Contabilidade
  }

}

class IT extends Employee {
  // ...
}
```

Cada Employee nesta estrutura social tem um único local onde podemos ir para ajustar seu respectivo algoritmo com maior probabilidade de mudança.

O ponto principal é separar a responsabilidade com base na estrutura social dos usuários que usam a aplicação.

## **OCP: Open-Closed Principle**

<img src="https://miro.medium.com/max/1400/1*SpU6T6Zr6OjeD4utxvZzQQ.jpeg"/>

Considerado o princípio mais importante do design de orientação a objeto, basicamente, o OCP nos diz que:

> Um artefato de software deve ser aberto para extensão, mas fechado para modificação.

Em geral, este princípio é apenas sobre escrever seu codigo de uma maneira que quando você precisar adicionar uma nova funcionalidade, não vai ser necessário alterar um código existente.

Para fazer isso, escrevemos interfaces e classes abstratas para ditar a política de nível superior que precisa ser implementada e, em seguida, implementamos essa política usando classes concretas.


Vamos supor que tenhamos que desenvolver uma integração para envio de email utilizando a SendGrid. Então, desenvolvemos uma classe concreta conectando-se com a API da SendGrid:

```typescript
class SendGridService {
    constructor (
      private readonly sendgridInstance: SendGridInstance
    ) { }

    public async sendMail(from: string, to: string, body: string): Promise<SendGridResult> {
        // formata o input que a API da send grid aceita
        // envia o email
        // cria o objeto de resposta
    }
}

```

Alguns meses depois, surge a necessidade de fazer uma alteração para substituir a SendGrid pela Mail Chimp.

Então vamos criar uma class `MailChimpService` concreta. Mas para conectá-lo ao nosso código, teremos que mudar bastante coisas aonde é utilizado o envio de email.

Como poderíamos projetar isso melhor?

Seguindo o OCP, poderíamos definir uma interface que especifica o que um serviço de email pode fazer e deixar a implementação atual para ser descoberta separadamente.

```typescript
// IEmailService.ts
export interface IMailServiceResult {
    success: boolean
  	error?: {
      message?: string
      code: string
    }
}

export interface IMail {
    from: string
    to: string
    body: string
}

export interface IMailService {
    sendMail(mail: IMail): Promise<IMailServiceResult>
}

// MailChimpService.ts
export class MailChimpService extends IMailService {
    public async sendMail(mail: Mail): Promise<IMailServiceResult> {
        // Integração com a API da MailChimp
    }
}
```

A ideia principal é manter a política separada dos detalhes para permitir o acoplamento livre.

> Os componentes de nível superior são protegidos contra alterações nos componentes de nível inferior.

Isso anda de mãos dadas com o DIP (Dependency Inversion Principle) de depender de uma interface em vez de classes concretas, e perto com o LSP (Liskov Substitution Principle) em termos de poder trocar implementações desde que o mesmo tipo/interface esteja sendo dependente.

## **LSP: Liskov Substitution Principle**

<img src="https://blog.larapulse.com/files/original/images/94/e2/94e27bbfb0c7ce8717ea3ac48af39a1dc5755507.png"/>

O LSP afirma que em um programa orientado a objetos, se substituirmos uma referência de objeto da superclasse por um objeto de qualquer uma de suas subclasses, o programa não deve quebrar.


Aproveitando o exemplo de Email Service, uma vez que já definimos o IMailService, podemos criar várias implementações de envio de email com provedores diferentes, seguindo a regra (Interface) IMailService:

```typescript
class SendGridEmailService implements IMailService {
  sendMail(email: IMail): Promise<IEmailTransmissionResult> {
    // algorithm
  }
}

class MailChimpEmailService implements IMailService {
  sendMail(email: IMail): Promise<IEmailTransmissionResult> {
    // algorithm
  }
}

class MailGunEmailService implements IMailService {
  sendMail(email: IMail): Promise<IEmailTransmissionResult> {
    // algorithm
  }
}
```

E então podemos injetar dependência em nossas classes, garantindo de que nos referimos à interface à qual ela pertence, em vez de injetar uma das implementações concretas.

```typescript
class CreateUserController extends BaseController {
  constructor (private readonly emailService: IEmailService) { }

  protected async execute(params): Promise<void> {
    // handle request
    
    // send mail
    const mail = new Mail({
      // ...
    })

    await this.emailService.sendMail(mail);
  }
}
```

Agora, todas essas implementações são válidas:

```typescript
const mailGunService = new MailGunEmailService();
const mailchimpService = new MailChimpEmailService();
const sendgridService = new SendGridEmailService();

// Todas são válidas:
const createUserController = new CreateUserController(mailGunService);
// ou
const createUserController = new CreateUserController(mailchimpService);
// ou
const createUserController = new CreateUserController(sendgridService);
```

Estamos aderindo ao LSP quando podemos trocar qual implementação de IEmailService estaremos usando.

## **ISP: Interface Segregation Principle**

<img src="https://i1.wp.com/www.topjavatutorial.com/wp-content/uploads/2016/02/Interface-Segregation-Principle.jpg?w=750&ssl=1"/>

> Classes não devem depender de coisas que elas não precisam.


Para evitar isso, devemos nos certificar de realmente dividir a funcionalidade exclusiva em interfaces.

Vamos supor que tenhamos 3 diferentes classes de *User* que utiliza 3 diferentes métodos da classe *Operations*, e para cada classe de *User*, nós estamos dependendo de duas funções que não precisamos. Exemplo:

<img src="https://khalilstemmler.com/img/blog/solid/isp.svg"/>

Parece não ser um problema tão grave, mas se precisarmos de injetar uma dependencia para cada função funcionar corretamente, teremos que injetar todas essas dependencias necessárias nas classes que não utilizam essas funções. Por exemplo, o constructor da classe Operations seria assim:

```typescript
class Operations {
  constructor (
    userRepo: IUserRepo, // Usada por todas funções
    emailService: IEmailService,  // Usada apenas por uma das funções
    authService: IAuthService, // Usada por duas funções
    redisService: IRedisService, // Usada por todas funções
  ) {}

  public operation1() {
    // ...
    this.userRepo.save({})
  }

  public operation2() {
    // ...
    this.emailService.sendMail({})
    this.userRepo.save({})
  }

  public operation3() {
    // ...
    this.userRepo.save()
    this.redisService.save({})
  }
}
```

E aplicando o ISP, esta estrutura ficaria assim:

<img src="https://khalilstemmler.com/img/blog/solid/isp-2.svg"/>

E agora, se era bastante complicado criar a classe Operations por conta que ela dependia de várias coisas, pegando a classe User1 como exemplo, nós seríamos capazes de criar uma classe que implementa SOMENTE da interface U1Ops.

```typescript
class User1Operations implements U1Ops {
  constructor (private readonly userRepo: IUserRepo) { }
  
  public async operation1() {
    await this.userRepo.save({})
  }
}
```

Agora a classe User1 depende apenas da classe User1Operations e não contém mais todo o "lixo" da classe Operations.

## **DIP: Dependency Inversion Principle**

<img src="https://blog.larapulse.com/files/original/images/56/42/564282d4aebf20542b8d78532e6a2d916847af15.png"/>

> Abstrações não devem depender de detalhes. Os detalhes devem depender das abstrações.

Detalhes? Abstrações?

Relembrando... Detalhes = Implementação, classes concretas. Abstrações = regras, interfaces, classes abstratas.

Ou seja, em vez de fazer isso:

```typescript
export interface ITacoService {
  // Estamos referenciando a classe concreta "CommomUser" em uma interface.
  sendTacos(userSource: CommomUser, userDestination: CommomUser, amount: number): Promise<SendTacosResult>
}
```

Fazemos assim:

```typescript
export interface ITacoService {
  // nenhuma referencia a classes concretas, apenas abstrações e interfaces
  sendTacos(userSource: IUser, userDestination: IUser, amount: number): Promise<ISendTacosResult>
}
```

Classes concretas também não devem depender de outras classes concretas. 
Isso é o que nos dá a capacidade de testar o código, porque deixamos o poder para o implementador passar uma dependência simulada se não quisermos fazer chamadas de API ou confiar em algo que não estamos interessados em testar no momento. Então, podemos fazer isso:

```typescript
class CreateUserController extends BaseController {
  constructor (private readonly emailService: IMailService) { } //Abstração

  protected execute(): void {
    // handle request
    this.emailService.sendMail({})
  }
}
```

E não isso:

```typescript
class CreateUserController extends BaseController {
  constructor (private readonly emailService: SendGridService) { } // <- concreção

  protected execute(): void {
    // handle request
    this.emailService.sendMail({})
  }
}
```

ou pior ainda:

```typescript
class CreateUserController extends BaseController {
  private emailService: SendGridService
  
  constructor () {
    // Precisariamos fazer um malabarismo macabro para mockar essa dependencia
    this.emailService = new SendGridService()
  }

  protected execute(): void {
    // handle request
    this.emailService.sendMail({})
  }
}
```

Referencias:
https://khalilstemmler.com/articles/solid-principles/solid-typescript/

https://wanago.io/2020/02/03/applying-solid-principles-to-your-typescript-code/

https://thinkdifferent0.wordpress.com/2017/07/07/solid-lsp-liskov-substitution-principle/

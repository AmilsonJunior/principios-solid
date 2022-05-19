# SOLID

> Os príncipios SOLID nos dizem como organizar as funções e estruturas de dados em classes e como essas classes devem ser interconectadas.

A palavra *classe* não implica em que esses principios sejam aplicáveis apenas ao paradigma de orientação a objetos. *classe* é apenas um agrupamento acoplado de funções e dados.

## Objetivo
O objetivo dos princípios é a criação de estruturas de software de nivel médio que:

- Tolerem mudanças;
- Sejam fáceis de entender;
- Sejam a base de componentes que possam ser usados em muitos sistemas de software;



Bons sistemas começam com um código limpo. Por um lado, se os tijolos não são bem feitos, a arquitetura da construção perde a importancia. Por outro lado, podemos fazer uma bagunça considerável com tijolos bem feitos. É aí que entram os principios SOLID.


## SRP: Single Responsibility Principle
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

## OCP: Open-Closed Principle
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
    // ...
}

export interface Mail {
    from: string
    to: string
    body: string
}

export interface IMailService {
    sendMail(mail: Mail): Promise<IMailServiceResult>
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

## LSP: Liskov Substitution Principle

<img src="https://thinkdifferent0.files.wordpress.com/2017/07/liskovsubtitutionprinciple_52bb5162.jpg"/>





Referencias:
https://khalilstemmler.com/articles/solid-principles/solid-typescript/

https://wanago.io/2020/02/03/applying-solid-principles-to-your-typescript-code/

https://thinkdifferent0.wordpress.com/2017/07/07/solid-lsp-liskov-substitution-principle/
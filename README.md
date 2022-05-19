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
<img src="http://brendan.enrick.com/image.axd?picture=OpenClosedPrinciple_thumb.jpg"/>
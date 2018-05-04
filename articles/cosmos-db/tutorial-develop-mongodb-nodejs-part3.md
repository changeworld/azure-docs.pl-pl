---
title: Samouczek bazy danych MongoDB, platformy Angular i języka Node dla platformy Azure — część 3 | Microsoft Doc
description: Część 3 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB za pomocą platformy Angular i języka Node w usłudze Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de645f46a889ba05fc54b1c5d2b9da64393d348e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-3-build-the-ui-with-angular"></a>Tworzenie aplikacji bazy danych MongoDB za pomocą platformy Angular i usługi Azure Cosmos DB — część 3: Tworzenie interfejsu użytkownika z użyciem platformy Angular

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację [interfejsu API bazy danych MongoDB](mongodb-introduction.md) napisaną w języku Node.js za pomocą programu Express i platformy Angular, a następnie podłączyć ją do własnej bazy danych usługi Azure Cosmos DB.

Część 3 samouczka jest oparta na [części 2](tutorial-develop-mongodb-nodejs-part2.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Zbudowanie interfejsu użytkownika platformy Angular
> * Wykorzystanie arkuszy CSS, aby ustawić wygląd i działanie
> * Lokalne przetestowanie aplikacji

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka upewnij się, że zostały wykonane czynności opisane w [części 2](tutorial-develop-mongodb-nodejs-part2.md) samouczka.

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika

1. W programie Visual Studio Code kliknij przycisk Zatrzymaj ![Przycisk Zatrzymaj w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png) w celu zatrzymania aplikacji w języku Node.

2. W oknie wiersza polecenia systemu Windows lub oknie terminalu systemu Mac wprowadź następujące polecenie, aby wygenerować składnik heroes. W tym kodzie g = wygeneruj, c = składnik, heroes = nazwa składnika, a ponadto jest używana struktura prostych plików (— flat), aby dla tego składnika nie został utworzony podfolder.

    ```
    ng g c heroes --flat 
    ```

    Okno terminalu pokazuje potwierdzenie dla nowych składników.

    ```bash
    installing component
      create src\client\app\heroes.component.ts
      update src\client\app\app.module.ts 
    ```

    Spójrzmy na pliki, które zostały utworzone i zaktualizowane. 

3. W programie Visual Studio Code w okienku **Eksplorator** przejdź do nowego folderu **src\client\app** i otwórz nowy plik **heroes.component.ts** utworzony w kroku 2. Ten plik składnika TypeScript został utworzony przez poprzednie polecenie.

    > [!TIP]
    > Jeśli folder aplikacji nie jest wyświetlany w programie Visual Studio Code, naciśnij klawisze CMD + SHIFT + P na komputerze Mac lub Ctrl + Shift + P w systemie Windows, aby otworzyć paletę poleceń, a następnie wpisz polecenie *Reload Window*, aby pobrać zmianę z systemu.

    ![Otwórz plik heroes.component.ts](./media/tutorial-develop-mongodb-nodejs-part3/open-folder.png)

4. W tym samym folderze otwórz plik **app.module.ts** i zwróć uwagę, że do deklaracji w wierszu 5 dodano element `HeroesComponent` oraz zaimportowano go również w wierszu 10.

    ![Otwórz plik app-module.ts](./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png)

    Teraz, gdy masz składnik Heroes, utwórz nowy plik HTML dla składnika heroes. Ponieważ utworzyliśmy minimalną aplikację, plik HTML miał trafić do tego samego pliku co plik TypeScript, ale chcemy go rozbić i utworzyć oddzielny plik.

5. W okienku **Eksplorator** kliknij prawym przyciskiem myszy folder **app**, kliknij pozycję **Nowy plik** i nadaj nowemu plikowi nazwę *heroes.component.html*.

6. W pliku **heroes.component.ts** usuń wiersze od 5 do 9 

    ```ts
    template: `
        <p>
          heroes Works!
        </p>
      `,
      ```
      i zastąp je tekstem
  
    ```ts
    templateUrl: './heroes.component.html',
    ```

    w celu odwoływania się do nowego pliku HTML.
 
    > [!TIP]
    > Możesz użyć rozszerzeń i fragmentów kodu Angular Essentials Johna Papy dla programu Visual Studio Code, aby przyspieszyć programowanie. 
    > 1. Kliknij przycisk **Rozszerzenia** ![przycisk Rozszerzenia w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/extensions-button.png).
    > 2. W polu wyszukiwania wpisz *angular essentials*.
    > 3. Kliknij pozycję **Zainstaluj**. 
    > 4. Kliknij przycisk **Załaduj ponownie**, aby użyć nowych rozszerzeń.
    > lub pobierz z witryny [http://jpapa.me/angularessentials](http://jpapa.me/angularessentials). 
    > ![Rozszerzenie Angular Essentials](./media/tutorial-develop-mongodb-nodejs-part3/angular-essentials-extension.png)

7. Wróć do pliku **heroes.component.html** i skopiuj do niego ten kod. Element `<div>` jest kontenerem całej strony. Wewnątrz tego kontenera znajduje się lista elementów hero, które musimy utworzyć w taki sposób, aby po kliknięciu jednego z nich można było go zaznaczyć, a następnie edytować lub usunąć w interfejsie użytkownika. Następnie w kodzie HTML mamy trochę zmieniania stylu, aby było wiadomo, który element został wybrany. Jest tam też obszar edycji, aby można było dodać nowy element hero lub edytować istniejący. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

8. Teraz, gdy mamy już kod HTML, musimy dodać go do pliku **heroes.component.ts**, aby umożliwić interakcję z szablonem. Nowy kod dodany do pliku **heroes.component.ts** poniżej dodaje szablon do naszego pliku składnika. Został dodany konstruktor, który pobiera niektóre elementy hero i inicjuje składnik usługi hero, aby pobrać wszystkie dane. Ten kod dodaje również wszystkie metody niezbędne do obsługi zdarzeń w interfejsie użytkownika. Możesz skopiować następujący kod w miejsce istniejącego kodu w pliku **heroes.component.ts**. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html'
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

9. W okienku **Eksplorator** otwórz plik **app/app.module.ts** i zaktualizuj wiersze 13 (dodaj przecinek) oraz 14, aby dodać import dla modułu `FormsModule`. Sekcja importu powinna teraz wyglądać tak jak poniżej:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

10. Dodaj import dla nowego modułu FormsModule w wierszu 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Wykorzystanie arkuszy CSS, aby ustawić wygląd i działanie

1. W okienku Eksplorator otwórz plik **src/client/styles.scss**.

2. Skopiuj następujący kod do pliku **styles.scss**, zastępując istniejącą zawartość pliku.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Zapisz plik. 

## <a name="display-the-component"></a>Wyświetlanie składnika

Teraz, gdy mamy składnik, jak możemy doprowadzić do wyświetlenia go na ekranie? Zmodyfikujmy domyślne składniki w pliku **app.component.ts**.

1. W okienku Eksplorator otwórz plik **client/app/app.component.ts**.

2. W wierszach od 6 do 8 zmień tytuł na Heroes, a następnie umieść nazwę składnika utworzonego w pliku **heroes.components.ts** (app-heroes) tak, aby odwoływał się do tego nowego składnika. Sekcja szablonu powinna teraz wyglądać tak jak poniżej: 

    ```ts
    template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `,
    ```

3. Istnieją inne składniki w pliku **heroes.components.ts**, do których się odwołujemy, takie jak składnik Hero, więc musimy je też utworzyć. W interfejsie wiersza polecenia platformy Angular użyj następującego polecenia, aby utworzyć model składnika hero i plik o nazwie **hero.ts**, gdzie g = wygeneruj, cl = klasa i hero = nazwa klasy.

    ```bash
    ng g cl hero
    ```

    Okno terminalu pokazuje potwierdzenie dla nowej klasy.

    ```bash
    installing class
    create src\client\app\hero.ts
    ```

4. W okienku Eksplorator otwórz plik **src\client\app\hero.ts**.

5. W pliku **hero.ts** zastąp zawartość pliku następującym kodem, który dodaje klasę Hero zawierającą identyfikator, nazwę i powiedzenie. 

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

6. Wróć do pliku **heroes.components.ts** i zwróć uwagę, że w wierszu `selectedHero: Hero;` (wiersz 10) składnik `Hero` jest podkreślony czerwoną linią. 

7. Kliknij lewym przyciskiem myszy termin `Hero`, a program Visual Studio wyświetli ikonę żarówki po lewej stronie bloku kodu. 

    ![Żarówka w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png)

8. Kliknij ikonę żarówki, a następnie kliknij pozycję **Importuj element Hero z folderu „client/app/hero”** lub **Importuj element Hero z folderu „./hero”**. (Komunikat zmienia się w zależności od konfiguracji).

    W wierszu 2 pojawi się nowy wiersz kodu. Jeśli wiersz 2 odwołuje się do folderu client/app/hero, zmodyfikuj go tak, aby odwoływał się do pliku hero z folderu lokalnego (./hero). Wiersz 2 powinien wyglądać następująco:

   ```
   import { Hero } from "./hero";
   ``` 

    Mamy już załatwioną sprawę modelu, ale nadal musimy utworzyć usługę.

## <a name="create-the-service"></a>Tworzenie usługi

1. W interfejsie wiersza polecenia platformy Angular wprowadź następujące polecenie, aby utworzyć usługę hero w pliku **app.module.ts**, gdzie g = wygeneruj, s = usługa, hero = nazwa usługi, -m = umieść w app.module.

    ```bash
    ng g s hero -m app.module
    ```

    Dane wyjściowe zawierają informacje, że plik **hero.service.ts** został utworzony, a plik **app.module.ts** został zaktualizowany.
  
    ```bash
    installing service
      create src\client\app\hero.service.ts
      update src\client\app\app.module.ts
    ```
    
    W pliku app.module.ts zostały dodane następujące wiersze kodu (wiersze 6 i 17):
    
    ```typescript
    import { HeroService } from './hero.service';
    ...
        providers: [HeroService],
    ```

2. W programie Visual Studio Code otwórz plik **hero.service.ts** i skopiuj do niego następujący kod, zastępując zawartość pliku.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Ten kod wykorzystuje najnowszą wersję klienta HttpClient oferowaną przez platformę Angular, który jest modułem, który musisz dostarczyć, więc zrobimy to w następnym kroku.

3. W programie Visual Studio Code otwórz plik **app.module.ts** i zaimportuj moduł HttpClientModule, aktualizując sekcję importu tak, aby zawierała moduł HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

4. W pliku **app.module.ts** dodaj instrukcję importu modułu HttpClientModule do listy importów.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

5. W programie Visual Studio Code wróć do pliku **heroes.components.ts**. Zwróć uwagę, że w wierszu `constructor(private heroService: HeroService) {}` (wiersz 13) element `HeroService` jest podkreślony czerwoną linią. Kliknij pozycję `HeroService`, a z lewej strony bloku kodu pojawi się żarówka. Kliknij żarówkę, a następnie kliknij pozycję **Importuj element HeroService z folderu „./hero.service ”** lub **Importuj element HeroService z folderu „client/app/hero.service ”**.

    Kliknięcie żarówki powoduje wstawienie nowego wiersza kodu w wierszu 2. Jeśli wiersz 2 odwołuje się do folderu client/app/hero.service, zmodyfikuj go tak, aby odwoływał się do pliku hero z folderu lokalnego (./hero.service). Wiersz 2 powinien wyglądać następująco:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

6. Zapisz wszystkie pliki w programie Visual Studio Code.

## <a name="build-the-app"></a>Kompilacja aplikacji

1. W wierszu polecenia wprowadź następujące polecenie, aby skompilować aplikację platformy Angular. 

    ```bash
    ng b
    ``` 

    Jeśli wystąpią jakiekolwiek problemy, w oknie terminala zostaną wyświetlone informacje o plikach, które należy naprawić. Po zakończeniu kompilacji nowe pliki trafią do folderu **dist**. Jeśli chcesz, możesz przejrzeć nowe pliki w folderze **dist**.

    Teraz uruchommy aplikację.

2. W programie Visual Studio Code kliknij przycisk **Debugowanie** ![ikona Debugowanie w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png) po lewej stronie, następnie kliknij przycisk **Rozpocznij debugowanie** ![ikona Debugowanie w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png).

3. Teraz otwórz przeglądarkę internetową, przejdź pod adres **localhost:3000** i obejrzyj lokalnie uruchomioną aplikację.

     ![Aplikacja Hero uruchomiona lokalnie](./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png)

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Zbudowano interfejs użytkownika platformy Angular
> * Lokalnie przetestowano aplikację

Możesz przejść do następnej części samouczka, aby utworzyć konto usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-develop-mongodb-nodejs-part4.md)

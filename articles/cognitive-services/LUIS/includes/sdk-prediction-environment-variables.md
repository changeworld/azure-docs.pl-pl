---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772424"
---
### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

Korzystając z klucza środowiska wykonawczego i punktu końcowego środowiska uruchomieniowego, należy utworzyć zmienne środowiskowe do uwierzytelniania i dostępu:

* `LUIS_RUNTIME_KEY`- Klucz zasobu środowiska wykonawczego do uwierzytelniania żądań.
* `LUIS_RUNTIME_ENDPOINT`- Punkt końcowy środowiska wykonawczego skojarzony z kluczem.
* `LUIS_APP_ID`- Identyfikator aplikacji IoT usługi `df67dcdb-c37d-46af-88e1-8b97951ca1c2`LUIS jest .
* `LUIS_APP_SLOT_NAME` - `production`Lub`staging`

Jeśli zamierzasz skorzystać z tego przewodnika Szybki start, aby uzyskać dostęp do własnej aplikacji, musisz wykonać dodatkowe kroki:
* Tworzenie aplikacji i uzyskanie identyfikatora aplikacji
* Przypisywanie klucza środowiska wykonawczego do aplikacji w portalu usługi LUIS
* Przetestuj adres URL za pomocą przeglądarki, aby uzyskać dostęp do aplikacji

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych `source ~/.bashrc` uruchom je z okna konsoli, aby wprowadzić zmiany w życie.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych `source .bash_profile` uruchom je z okna konsoli, aby wprowadzić zmiany w życie.

***

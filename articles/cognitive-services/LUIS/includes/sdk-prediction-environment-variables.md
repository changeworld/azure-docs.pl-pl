---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772424"
---
### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Za pomocą klucza czasu wykonywania i punktu końcowego środowiska uruchomieniowego Utwórz zmienne środowiskowe do uwierzytelniania i dostępu:

* `LUIS_RUNTIME_KEY` — klucz zasobu środowiska uruchomieniowego do uwierzytelniania żądań.
* `LUIS_RUNTIME_ENDPOINT` — punkt końcowy środowiska uruchomieniowego skojarzony z kluczem.
* `LUIS_APP_ID` — identyfikator aplikacji Public LUIS IoT jest `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` lub `staging`

Jeśli zamierzasz korzystać z tego przewodnika Szybki Start, aby uzyskać dostęp do swojej aplikacji, musisz wykonać dodatkowe czynności:
* Utwórz aplikację i uzyskaj identyfikator aplikacji
* Przypisywanie klucza czasu wykonywania do aplikacji w portalu LUIS
* Przetestuj adres URL w przeglądarce, aby uzyskać dostęp do aplikacji

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych ponownie uruchom okno konsoli.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych Uruchom `source ~/.bashrc` z okna konsoli, aby zmiany zaczęły obowiązywać.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edytuj `.bash_profile`i Dodaj zmienną środowiskową:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych Uruchom `source .bash_profile` z okna konsoli, aby zmiany zaczęły obowiązywać.

***

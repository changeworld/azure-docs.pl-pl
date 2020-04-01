---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183391"
---
#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Wybierz **opcję Aplikacje**.
1. Wybierz aplikację *webapi1,* aby otworzyć jej stronę **Właściwości.**
1. Wybierz pozycję **Opublikowane zakresy**. Opublikowane zakresy mogą służyć do przyznania aplikacji klienckiej pewnych uprawnień do internetowego interfejsu API.
1. W **SCOPE**polu `demo.read`SCOPE , enter `Read access to the web API`, i **description**, wprowadź .
1. W **SCOPE**polu `demo.write`SCOPE , enter `Write access to the web API`, i **description**, wprowadź .
1. Wybierz **pozycję Zapisz**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz **rejestracje aplikacji (wersja zapoznawcza)**.
1. Wybierz aplikację *webapi1,* aby otworzyć jej stronę **Przegląd.**
1. W obszarze **Zarządzanie**wybierz pozycję **Uwidaczniaj interfejs API**.
1. Obok **pozycji Identyfikator aplikacji URI**wybierz łącze **Set.**
1. Zastąp wartość domyślną `api`(identyfikator GUID) na , a następnie wybierz pozycję **Zapisz**. Pełny identyfikator URI jest wyświetlany i `https://your-tenant-name.onmicrosoft.com/api`powinien być w formacie . Gdy aplikacja sieci web żąda tokenu dostępu dla interfejsu API, należy dodać ten identyfikator URI jako prefiks dla każdego zakresu zdefiniowanego dla interfejsu API.
1. W obszarze **Zakresy zdefiniowane przez ten interfejs API**wybierz pozycję Dodaj **zakres**.
1. Wprowadź następujące wartości, aby utworzyć zakres definiujący dostęp do odczytu do interfejsu API, a następnie wybierz pozycję **Dodaj zakres:**
    1. **Nazwa zakresu**:`demo.read`
    1. **Nazwa wyświetlana zgody administratora:**`Read access to demo API`
    1. **Opis zgody administratora**:`Allows read access to the demo API`
1. Wybierz **pozycję Dodaj zakres**, wprowadź następujące wartości, aby dodać zakres definiujący dostęp do zapisu do interfejsu API, a następnie wybierz pozycję Dodaj **zakres:**
    1. **Nazwa zakresu**:`demo.write`
    1. **Nazwa wyświetlana zgody administratora:**`Write access to demo API`
    1. **Opis zgody administratora**:`Allows write access to the demo API`
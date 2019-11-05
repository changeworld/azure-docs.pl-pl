---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 6cb0ef848bdeab35c971ebd1a0b14eca1dfe3001
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523446"
---
#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Na stronie Przegląd **zarejestrowanej aplikacji** wybierz pozycję **Ustawienia**.
1. W obszarze **dostęp do interfejsu API**wybierz pozycję **wymagane uprawnienia**.
1. Wybierz pozycję **Windows Azure Active Directory**.
1. W obszarze **uprawnienia aplikacji**wybierz pozycję **Odczytuj i Zapisz dane katalogu**.
1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**. Pełne propagowanie uprawnień może potrwać kilka minut.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz **Azure Active Directory Graf**.
1. Wybierz pozycję **Uprawnienia aplikacji**.
1. Rozwiń węzeł **katalog** , a następnie zaznacz pole wyboru **katalog. ReadWrite. All** .
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)** .
1. Wybierz konto administratora dzierżawy.
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan**. Propagowanie uprawnień może potrwać kilka minut.
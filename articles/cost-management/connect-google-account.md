---
title: Połącz konto Google Cloud Platform do rozwiązania Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: Połącz konto Google Cloud Platform, aby wyświetlić kosztów i użycia danych w raportach usługi Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 247d959abadc92d70bdd60555a090986743e9322
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002070"
---
# <a name="connect-a-google-cloud-platform-account"></a>Nawiązać połączenie z kontem Google Cloud Platform

Do rozwiązania Cloudyn można połączyć z istniejącym kontem usługi Google Cloud Platform. Po połączeniu konta platformy Cloudyn dane kosztów i użycia są dostępne w raportach usługi Cloudyn. Ten artykuł pomaga skonfigurować i połączyć z kontem Google z usługą Cloudyn.


## <a name="collect-project-information"></a>Zbieranie informacji o projekcie

Należy rozpocząć od zbierania informacji o projekcie.

1. Zaloguj się do konsoli usługi Google Cloud Platform na [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Przejrzyj informacje o projekcie, który chcesz dołączyć do rozwiązania Cloudyn i zanotuj **Nazwa projektu** i **identyfikator projektu**. Zachowaj informacje przydatne do wykonania kolejnych kroków.  
    ![Nazwa projektu i pokazany w konsoli usługi Google Cloud Platform identyfikator projektu](./media/connect-google-account/gcp-console01.png)
3. Jeśli rozliczeń nie jest włączone i połączone z projektem, należy utworzyć konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Utwórz nowe konto rozliczeniowe](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Włącz eksportowanie rozliczeń zasobnika magazynu

Cloudyn pobiera Twoich danych rozliczeniowych Google z zasobnika magazynu. Zachowaj **Nazwa zasobnika** i **przedrostek raportu** informacji do późniejszego użycia podczas rejestracji rozwiązania Cloudyn.

Za pomocą usłudze Google Cloud Storage do przechowywania raportów użycia spowoduje naliczenie opłaty minimalnej. Aby uzyskać więcej informacji, zobacz [cennik magazynu w chmurze](https://cloud.google.com/storage/pricing).

1. Jeśli nie włączono rozliczeń Eksport do pliku, postępuj zgodnie z instrukcjami w artykule [włączania rozliczeń Eksport do pliku](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Można użyć JSON lub CSV rozliczeń format eksportu.
2. W przeciwnym razie w konsoli usługi Google Cloud Platform, przejdź do **rozliczeń** > **eksportu rozliczeń**. Należy pamiętać, rozliczeniami **Nazwa zasobnika** i **przedrostek raportu**.  
    ![Rozliczenia eksportowanie informacji wyświetlany na stronie Eksportowanie rozliczeń](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Włącz interfejsy API platformy Google Cloud

Aby zebrać informacje użycia i zasobów, Cloudyn musi następujące Google Cloud Platform interfejsy API włączone:

- BigQuery interfejsu API
- Google Cloud SQL
- Usługa Google Cloud Datastore interfejsu API
- Usługa Google Cloud Storage
- Interfejsu API JSON usługi Google Cloud Storage
- Google Compute Engine interfejsu API

### <a name="enable-or-verify-apis"></a>Włącz lub upewnij się, interfejsów API

1. W konsoli usługi Google Cloud Platform wybierz projekt, który ma zostać zarejestrowany w rozwiązaniu cloudyn.
2. Przejdź do **interfejsów API i usługi** > **biblioteki**.
3. Użyj funkcji wyszukiwania można znaleźć każdą wcześniej wymienione interfejsu API.
4. Dla każdego interfejsu API upewnij się, że **włączony interfejs API** jest wyświetlany. W przeciwnym razie kliknij przycisk **Włącz**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Dodawanie konta usługi Google Cloud do rozwiązania Cloudyn

1. Otwórz Cloudyn portal z witryny Azure portal lub przejdź do [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) i zaloguj się.
2. Kliknij przycisk **ustawienia** (symbol koła zębatego), a następnie wybierz **kont w chmurze**.
3. W **Zarządzanie kontami**, wybierz opcję **kont Google** kartę, a następnie kliknij przycisk **Dodaj nowe +** .
4. W **nazwa konta Google**, wprowadź adres e-mail konta rozliczeniowego, a następnie kliknij przycisk **dalej**.
5. W oknie dialogowym uwierzytelniania Google, wybierz lub wprowadź konto Google i następnie **Zezwalaj** cloudyn.com dostęp do Twojego konta.
6. Dodaj informacje o projekcie żądania zauważyć, że mamy poprzedniego. Obejmują one **identyfikator projektu**, **projektu** nazwy **rozliczeń** Nazwa zasobnika i **rozliczeń pliku** prefiks raportu, a następnie kliknij przycisk  **Zapisz**.  
    ![Dodaj projekt Google do konta platformy Cloudyn](./media/connect-google-account/add-project.png)

Konto Google, który pojawia się na liście kont i powinna być widoczna nazwa **uwierzytelniony**. Znajdujący się w nim swoją nazwę projektu Google i identyfikator należy pojawiają się i ma zielony znacznik wyboru. Stan konta powinna być widoczna nazwa **Ukończono**.

W ciągu kilku godzin Cloudyn raporty zawierają informacje dotyczące kosztów i użycia Google.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat platformy Cloudyn, przejdź do [przeglądanie użycia i kosztów](./tutorial-review-usage.md) samouczek dotyczący platformy Cloudyn.

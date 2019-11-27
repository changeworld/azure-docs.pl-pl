---
title: Połącz konto Google Cloud Platform do rozwiązania Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: Połącz konto Google Cloud Platform, aby wyświetlić kosztów i użycia danych w raportach usługi Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230133"
---
# <a name="connect-a-google-cloud-platform-account"></a>Nawiązać połączenie z kontem Google Cloud Platform

Do rozwiązania Cloudyn można połączyć z istniejącym kontem usługi Google Cloud Platform. Po połączeniu konta platformy Cloudyn dane kosztów i użycia są dostępne w raportach usługi Cloudyn. Ten artykuł pomaga skonfigurować i połączyć z kontem Google z usługą Cloudyn.


## <a name="collect-project-information"></a>Zbieranie informacji o projekcie

Należy rozpocząć od zbierania informacji o projekcie.

1. Zaloguj się do konsoli Google Cloud Platform w [https://console.cloud.google.com](https://console.cloud.google.com).
2. Przejrzyj informacje o projekcie, które chcesz dołączyć do Cloudyn i zanotuj **nazwę projektu** i **Identyfikator projektu**. Zachowaj informacje przydatne do wykonania kolejnych kroków.  
    ![nazwę projektu i identyfikator projektu widoczne w konsoli Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Jeśli rozliczeń nie jest włączone i połączone z projektem, należy utworzyć konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Tworzenie nowego konta rozliczeniowego](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Włącz eksportowanie rozliczeń zasobnika magazynu

Cloudyn pobiera Twoich danych rozliczeniowych Google z zasobnika magazynu. Informacje o **nazwie zasobnika** i **przedrostka raportu** są przydatne do późniejszego użycia podczas rejestracji Cloudyn.

Za pomocą usłudze Google Cloud Storage do przechowywania raportów użycia spowoduje naliczenie opłaty minimalnej. Aby uzyskać więcej informacji, zobacz [Cennik usługi Cloud Storage](https://cloud.google.com/storage/pricing).

1. Jeśli nie włączono eksportowania rozliczeń do pliku, postępuj zgodnie z instrukcjami dotyczącymi [sposobu włączania eksportowania rozliczeń do pliku](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Można użyć JSON lub CSV rozliczeń format eksportu.
2. W przeciwnym razie w konsoli Google Cloud Platform przejdź do **Rozliczeń** > **Eksportuj rozliczenia**. Zanotuj **nazwę zasobnika** rozliczeniowego i **prefiks raportu**.  
    ![informacje o eksporcie dotyczące rozliczeń widoczne na stronie eksportowania rozliczeń](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Włącz interfejsy API platformy Google Cloud

Aby zebrać informacje użycia i zasobów, Cloudyn musi następujące Google Cloud Platform interfejsy API włączone:

- BigQuery interfejsu API
- Google Cloud SQL
- Usługa Google Cloud Datastore interfejsu API
- Google Cloud Storage
- Interfejsu API JSON usługi Google Cloud Storage
- Google Compute Engine interfejsu API

### <a name="enable-or-verify-apis"></a>Włącz lub upewnij się, interfejsów API

1. W konsoli usługi Google Cloud Platform wybierz projekt, który ma zostać zarejestrowany w rozwiązaniu cloudyn.
2. Przejdź do **interfejsów api & Services** > **Library**.
3. Użyj funkcji wyszukiwania można znaleźć każdą wcześniej wymienione interfejsu API.
4. Dla każdego interfejsu API Sprawdź, czy jest wyświetlany **interfejs API włączony** . W przeciwnym razie kliknij pozycję **Włącz**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Dodawanie konta usługi Google Cloud do rozwiązania Cloudyn

1. Otwórz Portal Cloudyn w Azure Portal lub przejdź do [https://azure.cloudyn.com](https://azure.cloudyn.com/) i zaloguj się.
2. Kliknij pozycję **Ustawienia** (symbol koło zębate), a następnie wybierz pozycję **konta w chmurze**.
3. W obszarze **Zarządzanie kontami**wybierz kartę **konta Google** , a następnie kliknij pozycję **Dodaj nowe +** .
4. W polu **nazwa konta Google**wprowadź adres e-mail konta rozliczeniowego, a następnie kliknij przycisk **dalej**.
5. W oknie dialogowym Uwierzytelnianie Google wybierz lub wprowadź konto Google, a następnie **Zezwól na** Cloudyn.com dostępu do swojego konta.
6. Dodaj informacje o projekcie żądania zauważyć, że mamy poprzedniego. Obejmują one **Identyfikator projektu**, nazwę **projektu** , nazwę zasobnika **rozliczeń** i prefiks raportu **pliku rozliczeń** , a następnie kliknij przycisk **Zapisz**.  
    ![dodać usługi Google Project do konta Cloudyn](./media/connect-google-account/add-project.png)

Twoje konto Google zostanie wyświetlone na liście kont i powinno być **uwierzytelniane**. Znajdujący się w nim swoją nazwę projektu Google i identyfikator należy pojawiają się i ma zielony znacznik wyboru. Stan konta powinien powiedzieć **zakończony**.

W ciągu kilku godzin Cloudyn raporty zawierają informacje dotyczące kosztów i użycia Google.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat Cloudyn, przejdź do samouczka [Przegląd użycia i kosztów](./tutorial-review-usage.md) dla Cloudyn.

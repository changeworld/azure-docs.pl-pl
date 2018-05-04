---
title: Konto usługi Google Cloud Platform połączyć z usługą Azure koszt Management | Dokumentacja firmy Microsoft
description: Połącz konta usługi Google Cloud Platform, aby wyświetlić kosztów i repots danych użycia w kosztów zarządzania.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b7141d028c496b75b061c17aa19ad6c73c0588c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Połącz z kontem usługi Google Cloud Platform

Możesz połączyć istniejącego konta usługi Google Cloud Platform do usługi Azure Management kosztów. Po nawiązaniu połączenia koszt zarządzania kontem kosztów i dane użycia jest dostępne w raportach kosztów zarządzania. Ten artykuł pomaga skonfigurować i Połącz konto Google z kosztów zarządzania.

## <a name="collect-project-information"></a>Zbieranie informacji o projekcie

Należy rozpocząć od zbieranie informacji o projekcie.

1. Zaloguj się do konsoli usługi Google Cloud Platform pod adresem [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Przejrzyj informacje projektu do których chcesz dołączyć kosztów zarządzania i Uwaga **Nazwa projektu** i **identyfikator projektu**. Zachowaj informacje przydatne do wykonania kolejnych kroków.  
    ![Konsola usługi Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Jeśli rozliczeń nie jest włączone i połączone z projektu, należy utworzyć konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Utwórz nowe konto rozliczeniowe](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Włącz eksportowanie rozliczeń zasobnika magazynu

Kosztów zarządzania pobiera programu Google rozliczeń danych z zasobnika magazynu. Zachowaj **nazwa pakietu** i **prefiks raport** informacje przydatne do późniejszego użytku podczas rejestracji kosztów zarządzania.

Przy użyciu usługi Google Cloud magazynu do przechowywania raportów użycia wiąże się z minimalnym opłat. Aby uzyskać więcej informacji, zobacz [cennik magazynu w chmurze](https://cloud.google.com/storage/pricing).

1. Jeśli rozliczeń Eksport do pliku nie jest włączone, wykonaj instrukcje w [włączania rozliczeń Eksport do pliku](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Można użyć rozliczeniowego JSON lub CSV format eksportu.
2. W przeciwnym razie przejdź w konsoli usługi Google Cloud Platform do **rozliczeń** > **eksportu rozliczeń**. Należy zwrócić uwagę rozliczeniowego **nazwa pakietu** i **prefiks raport**.  
    ![Eksport rozliczeń](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Włącz interfejsy API programu Google Cloud Platform

Aby zebrać informacje o użyciu i zasobów, kosztów wymagania w zakresie zarządzania Google Cloud Platform API włączone:

- BigQuery interfejsu API
- Google Cloud SQL
- Magazyn danych usługi Google Cloud interfejsu API
- Magazyn w chmurze Google
- Magazyn w chmurze Google JSON API
- Interfejs API aparatu obliczeń Google

### <a name="enable-or-verify-apis"></a>Włączanie lub sprawdź interfejsów API

1. W konsoli usługi Google Cloud Platform wybierz projekt, który chcesz zarejestrować w usłudze zarządzania kosztów.
2. Przejdź do **interfejsów API i usługi** > **biblioteki**.
3. Wyszukiwania można używać, aby znaleźć każdego wymienionego powyżej interfejsu API.
4. Dla każdego interfejsu API, upewnij się, że **interfejsu API włączone** jest wyświetlany. W przeciwnym razie kliknij przycisk **włączyć**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Dodaj konto Google Cloud do zarządzania koszt

1. Otwórz Cloudyn portal z portalu Azure lub przejdź do [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) i zaloguj się.
2. Kliknij przycisk **ustawienia** (symbol koło zębate), a następnie wybierz **kont chmury**.
3. W **Zarządzanie kontami**, wybierz pozycję **kont Google** a następnie kliknij pozycję **Dodaj nowy +**.
4. W **nazwa konta Google**, wprowadź adres e-mail dla konta rozliczeniowego, a następnie kliknij przycisk **dalej**.
5. W oknie dialogowym uwierzytelniania Google, wybierz lub wprowadź konto Google, a następnie **Zezwalaj** cloudyn.com dostęp do Twojego konta.
6. Dodaj informacje o projekcie żądania była poprzedniej zauważyć. Obejmują one **identyfikator projektu**, **projektu** nazwa **rozliczeń** nazwa pakietu i **rozliczeń pliku** raport prefiksu, a następnie kliknij przycisk  **Zapisz**.  
    ![Dodawanie projektu Google](./media/connect-google-account/add-project.png)

Konto Google pojawi się na liście kont i powinny przekazać komunikat **uwierzytelniany**. W obszarze, swoją nazwę projektu Google i identyfikator powinien się i mające zielony znacznik wyboru. Stan konta zostanie wyświetlony komunikat **Ukończono**.

W ciągu kilku godzin koszt zarządzania raporty zawierają informacje kosztów i użyciu Google.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat usługi Azure koszt zarządzania, w dalszym ciągu [Przejrzyj użycia i koszty](./tutorial-review-usage.md) samouczka kosztów zarządzania.

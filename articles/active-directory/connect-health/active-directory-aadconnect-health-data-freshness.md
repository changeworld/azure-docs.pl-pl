---
title: Azure AD Connect Health — dane usługi kondycji nie jest do alertu Data | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano przyczyny alertu "dane usługi kondycji nie jest aktualny" i jak rozwiązać problemy.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi kondycji nie jest aktualny alertu

## <a name="overview"></a>Przegląd
<li>Azure AD Connect Health generuje alert świeże dane w przypadku nie otrzyma punktów danych z serwera do dwóch godzin. Tytuł alertu jest **dane usługi kondycji nie jest aktualny**. </li>
<li>**Ostrzeżenie** alert stanu generowane, jeśli Connect Health nie odbiera elementy częściowe dane wysyłane z serwera do dwóch godzin. Alert ostrzeżenia o stanie wyzwalają powiadomienia e-mail do administratora dzierżawy. </li>
<li>**Błąd** alert stanu generowane, jeśli Connect Health nie otrzymuje żadnych elementów danych wysłanych z serwera do dwóch godzin. Błąd stanu alertu wywołuje wiadomości e-mail z powiadomieniami do administratora dzierżawy. </li>

>[!IMPORTANT] 
> Ten alert następuje Connect Health [zasady przechowywania danych](active-directory-aadconnect-health-gdpr.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 
* Upewnij się, że przekazywane i spełniają [sekcji wymagania](active-directory-aadconnect-health-agent-install.md#requirements).
* Użyj [narzędzia test łączności](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do wykrywania problemów z łącznością.


## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health — często zadawane pytania](active-directory-aadconnect-health-faq.md)

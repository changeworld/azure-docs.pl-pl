---
title: Program Azure AD Connect Health — dane usługi kondycji nie jest maksymalnie alert Data | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315098"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi kondycji nie są na bieżąco alert

## <a name="overview"></a>Przegląd
<li>Program Azure AD Connect Health umożliwia wygenerowanie alertu świeże danych podczas nie otrzyma wszystkich punktów danych z serwera przez dwie godziny. Tytuł alertu jest **dane usługi kondycji nie są na bieżąco**. </li>
<li>**Ostrzeżenie** stan alertu w przypadku programu Connect Health nie otrzyma elementy częściowe dane wysyłane z serwera przez dwie godziny. Alert stanu ostrzeżenia, nie będzie wyzwalać powiadomienia e-mail do administratora dzierżawy. </li>
<li>**Błąd** stan alertu w przypadku programu Connect Health nie otrzyma żadnych elementów dane wysyłane z serwera przez dwie godziny. Błąd stanu alertu wywołuje powiadomienia e-mail, aby administratora dzierżawy. </li>

>[!IMPORTANT] 
> Ten alert jest zgodna Connect Health [zasady przechowywania danych](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 
* Upewnij się, że omijają i spełniać [sekcji wymagania dotyczące](how-to-connect-health-agent-install.md#requirements).
* Użyj [narzędzia łączności test](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do wykrywania problemów z łącznością.


## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)

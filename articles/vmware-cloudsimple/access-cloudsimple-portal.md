---
title: Dostęp do rozwiązań VMware platformy Azure (Automatyczna synchronizacja) — Portal
description: Zawiera opis sposobu uzyskiwania dostępu do rozwiązań VMware platformy Azure (Automatyczna synchronizacja) z Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015954"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Dostęp do rozwiązań VMware platformy Azure (Automatyczna synchronizacja) z Azure Portal

Logowanie jednokrotne jest obsługiwane w przypadku dostępu do portalu automatycznej synchronizacji. Po zalogowaniu się do Azure Portal można uzyskać dostęp do portalu automatycznej synchronizacji bez ponownego logowania. Podczas pierwszego dostępu do portalu automatycznej synchronizacji zostanie wyświetlony monit o autoryzację aplikacji [autoryzacji usługi automatycznej synchronizacji](#consent-to-avs-service-authorization-application) . Autoryzacja to jednorazowa akcja.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użytkownicy z rolami wbudowanego **właściciela** i **współautor** mogą uzyskać dostęp do portalu automatycznej synchronizacji. Role należy skonfigurować w grupie zasobów, w której jest wdrażana usługa automatycznej synchronizacji. Role można także skonfigurować w obiekcie usługi automatycznej synchronizacji. Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz artykuł [Wyświetlanie przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Tylko użytkownicy z wbudowanymi rolami **właściciela** i **współautorów** mogą uzyskać dostęp do portalu automatycznej synchronizacji. Role muszą być skonfigurowane w ramach subskrypcji. Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz artykuł [Wyświetlanie przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

W przypadku korzystania z ról niestandardowych rola powinna mieć dowolną z następujących operacji w obszarze ```Actions```.  Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Jeśli którakolwiek z operacji jest częścią ```NotActions```, użytkownik nie może uzyskać dostępu do portalu automatycznej synchronizacji. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Dostęp do portalu automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi automatycznej synchronizacji**.

3. Wybierz usługę automatycznej synchronizacji, w której chcesz utworzyć chmurę prywatną.

4. Na stronie **Przegląd** kliknij pozycję **Przejdź do portalu automatycznej synchronizacji**. Jeśli po raz pierwszy uzyskujesz dostęp do portalu automatycznej synchronizacji z Azure Portal, zostanie wyświetlony monit o autoryzację aplikacji [autoryzacji usługi automatycznej synchronizacji](#consent-to-avs-service-authorization-application) . 

    ![Uruchom Portal automatycznej synchronizacji](media/launch-cloudsimple-portal.png)

> [!NOTE]
> W przypadku wybrania operacji chmury prywatnej (takiej jak utworzenie lub rozszerzenie chmury prywatnej) bezpośrednio z Azure Portal Portal automatycznej synchronizacji zostanie otwarty na wskazanej stronie.

W portalu automatycznej synchronizacji wybierz pozycję **Strona główna** w menu po stronie, aby wyświetlić podsumowanie informacji o chmurze prywatnej automatycznej synchronizacji. Wyświetlane są zasoby i pojemność chmury prywatnej do automatycznej synchronizacji oraz alerty i zadania, które wymagają uwagi. W przypadku typowych zadań kliknij nazwane ikony w górnej części strony.

![Strona główna](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Wyrażanie zgody na automatycznej synchronizacji aplikacji autoryzacji

Po pierwszym uruchomieniu portalu automatycznej synchronizacji w Azure Portal jest wymagana zgoda na aplikację autoryzacji usługi automatycznej synchronizacji. Wybierz pozycję **Akceptuj** , aby udzielić żądanych uprawnień i uzyskać dostęp do portalu automatycznej synchronizacji.

![Wyrażanie zgody na zadawanie automatycznej synchronizacji usługi — Administratorzy](media/cloudsimple-azure-consent.png)

Jeśli masz uprawnienia administratora globalnego, możesz wyrazić zgodę na Twoją organizację. Wybierz pozycję **wyrażanie zgody w imieniu organizacji**.

![Wyrażanie zgody na zadawanie automatycznej synchronizacji usługi — Administrator globalny](media/cloudsimple-azure-consent-global-admin.png)

Jeśli Twoje uprawnienia nie zezwalają na dostęp do portalu automatycznej synchronizacji, skontaktuj się z administratorem globalnym dzierżawy, aby przyznać wymagane uprawnienia. Administrator globalny może wyrazić zgodę w imieniu organizacji.

![Zgoda na zadawanie automatycznej synchronizacji usługi — wymaga od administratorów](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)

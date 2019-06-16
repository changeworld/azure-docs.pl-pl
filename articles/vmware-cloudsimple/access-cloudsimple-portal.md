---
title: Rozwiązanie Azure VMware dostępu przez CloudSimple — Portal
description: Opisuje, jak uzyskać dostęp do rozwiązań VMware przez portal CloudSimple z witryny Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c7bb080b350742d0722cdb4e07b82a6881ba05b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073666"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Uzyskiwanie dostępu do rozwiązań VMware przez portal CloudSimple z witryny Azure portal

Logowanie jednokrotne jest obsługiwana dla dostępu do portalu CloudSimple. Po zalogowaniu się do witryny Azure portal są dostępne w portalu CloudSimple bez ponownego logowania. Po raz pierwszy możesz uzyskać dostęp do portalu CloudSimple, zostanie wyświetlony monit autoryzować [CloudSimple usługi autoryzacji](#consent-to-cloudsimple-service-authorization-application) aplikacji.  Autoryzacja to akcja jednorazowa.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Tylko użytkownicy z wbudowanej **właściciela** i **Współautor** role mogą uzyskiwać dostęp do portalu CloudSimple.  Role musi być skonfigurowany w ramach subskrypcji.  Aby uzyskać więcej informacji na temat sprawdzania swojej roli, zobacz [wyświetlania przypisań roli](https://docs.microsoft.com/azure/role-based-access-control/check-access) artykułu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usług CloudSimple**.

3. Wybierz usługę CloudSimple, na którym chcesz utworzyć chmury prywatnej.

4. Na **Przegląd** kliknij **przejdź do portalu CloudSimple**.  Jeśli uzyskujesz dostęp do portalu CloudSimple w witrynie Azure portal, po raz pierwszy, zostanie wyświetlony monit do autoryzowania [CloudSimple usługi autoryzacji](#consent-to-cloudsimple-service-authorization-application) aplikacji. 

    ![Uruchom CloudSimple portal](media/launch-cloudsimple-portal.png)

> [!TIP]
> Jeśli wybierzesz operacją chmury prywatnej (np. Tworzenie lub zwiększyć pojemność chmury prywatnej) bezpośrednio z witryny Azure portal, CloudSimple portal otworzy stronę wskazane.

W portalu CloudSimple wybierz **Home** w menu po stronie, aby wyświetlić podsumowanie informacji na temat usługi chmury prywatnej. Zasoby i wydajność swojej chmury prywatnej są wyświetlane wraz z alertów i zadań, które wymagają uwagi. Do wykonywania typowych zadań kliknij przycisk o nazwie ikony w górnej części strony.

![Strona główna](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Zgoda na aplikację CloudSimple usługi autoryzacji

Uruchamianie portalu CloudSimple w witrynie Azure portal po raz pierwszy wymaga Twojej zgody dla aplikacji CloudSimple usługi autoryzacji.  Wybierz **Akceptuj** umożliwia udzielenie żądane uprawnienia i uzyskać dostęp do portalu CloudSimple. 

![Zgoda na CloudSimple usługi autoryzacji — Administratorzy](media/cloudsimple-azure-consent.png)

Jeśli masz uprawnienia administratora globalnego, klient wyraża zgodę dla Twojej organizacji.  Wybierz **zgody w imieniu swojej organizacji**.

![Zgoda na CloudSimple usługi autoryzacji — administratora globalnego](media/cloudsimple-azure-consent-global-admin.png)

Jeśli Twoje uprawnienia nie zezwalają na dostęp do portalu CloudSimple, skontaktuj się z administratorem globalnym dzierżawy, aby udzielić wymaganych uprawnień.  Administrator globalny może wyrażać zgodę w imieniu swojej organizacji.

![Zgoda na CloudSimple usługi autoryzacji — wymaga od administratorów](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md)
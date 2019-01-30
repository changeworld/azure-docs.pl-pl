---
title: Usługa App Service w usłudze Azure Stack — omówienie | Dokumentacja firmy Microsoft
description: Omówienie usługi App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: c2fe4f5377bfd73f327ef2d0ba8b5fdfff4fa8af
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243355"
---
# <a name="app-service-on-azure-stack-overview"></a>Omówienie usługi App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure App Service w usłudze Azure Stack to oferta typu platform-as-a-service (PaaS), Microsoft Azure dostępnych w usłudze Azure Stack. Usługa umożliwia klientom wewnętrzne lub zewnętrzne do tworzenia sieci web, interfejsów API i usługi Azure Functions aplikacje dla dowolnej platformy lub urządzenia. Mogą one integrować Twoje aplikacje z aplikacjami lokalnymi i automatyzować ich procesy biznesowe. Operatorzy chmury usługi Azure Stack mogą uruchamiać aplikacje klienta na w pełni zarządzanych maszynach wirtualnych za pomocą wybranych przez nich udostępnianych zasobów maszyn wirtualnych lub dedykowanych maszyn wirtualnych.

Usługa Azure App Service umożliwia automatyzowanie procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa Azure App Service pozwala na łączenie różnych składników, takich jak witryny sieci Web, interfejsów API REST i procesów biznesowych, w ramach jednego rozwiązania.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Dlaczego są oferowane usługi Azure App Service w usłudze Azure Stack?

Poniżej przedstawiono niektóre najważniejszych funkcji i możliwości usługi App Service:

- **Wiele języków i struktur**: Usługa App Service ma najwyższej jakości pomoc techniczna dla platformy ASP.NET, Node.js, Java, PHP i Python. Można również uruchomić program Windows PowerShell oraz inne skrypty lub pliki wykonywalne na maszynach wirtualnych usługi App Service.
- **Optymalizacja metodyki DevOps**: Konfigurowanie ciągłej integracji i ciągłego wdrażania za pomocą usługi GitHub, lokalnego narzędzia Git lub BitBucket. Promowanie aktualizacji za pośrednictwem środowisk testowych i przejściowych i zarządzanie aplikacjami w usłudze App Service przy użyciu programu Azure PowerShell lub wieloplatformowego interfejsu wiersza polecenia (CLI).
- **Integracja z programem Visual Studio**: Dedykowane narzędzia w programie Visual Studio usprawniają procesy tworzenia i wdrażania aplikacji.

## <a name="app-types-in-app-service"></a>Typy aplikacji w usłudze App Service

Usługa App Service oferuje kilka typów aplikacji, z których każdy jest przeznaczony do obsługi określonego obciążenia:

- [Aplikacje sieci Web](../app-service/overview.md) do hostowania witryn sieci Web i aplikacji sieci web.
- [Funkcja API Apps](../app-service/overview.md) do hostowania interfejsów API REST.
- Usługa Azure Functions do obsługi obciążeń bezserwerowych oparte na zdarzeniach.

Wyraz *aplikacji* odwołuje się do zasobów hostowania przeznaczonych do obsługi obciążenia. Biorąc *aplikacji sieci web* na przykład Użytkownicy przyzwyczajeni prawdopodobnie na myśl aplikacji sieci web, zgodnie z aplikacji i zasobów obliczeniowych kodu łącznie zapewnia funkcjonalność do przeglądarki. W usłudze App Service aplikacja sieci web jest z zasobów obliczeniowych usługi Azure Stack zapewnia hostowania kodu aplikacji.

Aplikacja może składać się z wielu aplikacji usługi App Service różnego rodzaju. Na przykład jeśli aplikacja składa się z frontonu internetowego interfejsu API REST ponownie zakończyć, możesz wykonywać następujące czynności:

- Wdrożyć oba rozwiązania (fronton i interfejs API) w jednej aplikacji internetowej.
- Wdrożyć kod frontonu w aplikacji internetowej i kod zaplecza w aplikacji interfejsu API.

   [ ![Omówienie usługi App Service przy użyciu funkcji monitorowania danych](media/azure-stack-app-service-overview/image01.png "Omówienie usługi App Service przy użyciu danych monitorowania") ](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Co to jest plan usługi App Service?

Dostawcy zasobów usługi App Service używa tego samego kodu używającego usługi Azure App Service i dlatego współużytkuje niektóre typowe pojęcia. W usłudze App Service cen kontener dla aplikacji o nazwie *planu usługi App Service*. Reprezentuje zestaw dedykowanych maszynach wirtualnych, używane do przechowywania aplikacji. W ramach danej subskrypcji może mieć wiele planów usługi App Service.

Na platformie Azure są udostępnione i dedykowane procesów roboczych. Udostępnionego procesu roboczego obsługuje hosting o dużej gęstości aplikacji wielodostępnych, a istnieje tylko jeden zestaw udostępniony pracowników. Dedykowane serwery są używane przez tylko jednej dzierżawy i są dostępne w trzech rozmiarach: mały, średni i duży. Potrzeby klientów w środowisku lokalnym nie zawsze opisany w taki sposób, przy użyciu tych terminów. W usłudze App Service w usłudze Azure Stack Administratorzy dostawcy zasobów można zdefiniować warstwy procesu roboczego, które mają być dostępne. Oparte na Twoje unikatowe potrzeby hostingu, można zdefiniować wiele zestawów pracowników udostępnionego lub różnych zestawów dedykowanych procesów roboczych. Korzystając z tych definicji warstwy procesu roboczego, ich można zdefiniować własne wycena jednostek SKU.

## <a name="portal-features"></a>Funkcje portalu

Usługa App Service w usłudze Azure Stack używa tego samego interfejsu użytkownika, który używa usługi Azure App Service. Dotyczy to na zapleczu. Jednak niektóre funkcje są wyłączone w usłudze Azure Stack. Oczekiwania specyficzne dla platformy Azure lub usługi, które wymagają tych funkcji nie są obecnie dostępne w usłudze Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

- [Przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Zainstaluj dostawcę zasobów usługi App Service](azure-stack-app-service-deploy.md)

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md), takich jak [dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md) i [dostawcy zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md).

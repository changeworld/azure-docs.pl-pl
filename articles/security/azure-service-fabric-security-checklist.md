---
title: Lista kontrolna zabezpieczeń sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw Lista kontrolna zabezpieczeń zabezpieczeń sieci szkieletowej Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ef404b106d600f5cb25a46319d75c8978148b466
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-service-fabric-security-checklist"></a>Lista kontrolna zabezpieczeń platformy Azure sieci szkieletowej usług
Ten artykuł zawiera listę kontrolną łatwy w użyciu, które mogą pomóc w zabezpieczeniu środowiska sieć szkieletowa usług Azure.

## <a name="introduction"></a>Wprowadzenie
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

## <a name="checklist"></a>Lista kontrolna
Poniższa lista kontrolna umożliwia daje pewność, że nie pomijane wszelkie istotne problemy w zarządzania i konfiguracji bezpieczna sieć szkieletowa usług Azure.


|Lista kontrolna kategorii| Opis |
| ------------ | -------- |
|[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Kontrola dostępu umożliwia administratora klastrów, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, co klaster bardziej bezpieczne.</li><li>Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). </li><li> Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.</li></ul>|
|[Certyfikaty X.509 i sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certyfikaty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) używane w klastrach uruchomionych obciążeń produkcyjnych powinny zostać utworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskane z zatwierdzonych [urzędu certyfikacji,](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nigdy nie używaj żadnego [tymczasowego lub certyfikaty testów](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) w środowisku produkcyjnym, które są tworzone przy użyciu narzędzi, takich jak [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Można użyć [certyfikatu z podpisem własnym](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) jednak zrobić to dla klastrów testowych, a nie w środowisku produkcyjnym.</li></ul>|
|[Zabezpieczenia klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Zabezpieczenia węzła do węzła, węzeł klient zabezpieczenia, są następujące scenariusze zabezpieczeń klastra [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Uwierzytelnianie klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Uwierzytelnia [komunikacji między węzłami](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) dla Federacji klastra. </li></ul>|
|[Uwierzytelnianie serwera](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Uwierzytelnia [klastra zarządzania punkty końcowe](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) do klienta zarządzania.</li></ul>|
|[Zabezpieczenia aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.</li><li>   Szyfrowanie danych w węzłach podczas replikacji.</li></ul>|
|[Certyfikat klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze.</li><li>    Odcisk palca certyfikatu podstawowego zestawu w sekcji odcisk palca i że w zmiennych ThumbprintSecondary pomocniczej.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Do uaktualnienia, można użyć dwóch różnych certyfikatów, podstawowego i pomocniczego.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Jest to zestaw certyfikatów, które chcesz zainstalować na klientach uwierzytelniony. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Ustaw nazwę pospolitą pierwszy certyfikat klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu. </li></ul>|
|ReverseProxyCertificate| <ul><li>Jest opcjonalny certyfikat, który może być określony, jeśli chcesz zabezpieczyć Twoje [wstecznego Proxy](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Usługa Key Vault| <ul><li>Pozwala zarządzać certyfikatami dla klastrów sieci szkieletowej usług platformy Azure.  </li></ul>|


## <a name="next-steps"></a>Kolejne kroki
- [Proces uaktualniania klastra sieci szkieletowej usług oraz oczekiwań od użytkownika](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Zarządzanie aplikacji usługi Service Fabric w programie Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Wprowadzenie modelu kondycji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).

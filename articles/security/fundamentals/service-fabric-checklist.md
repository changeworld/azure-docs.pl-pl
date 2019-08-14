---
title: Lista kontrolna zabezpieczeń usługi Azure Service Fabric | Microsoft Docs
description: Ten artykuł zawiera zestaw list kontrolnych dla zabezpieczeń sieci szkieletowej Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927728"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista kontrolna zabezpieczeń usługi Azure Service Fabric
Ten artykuł zawiera łatwą w użyciu listę kontrolną, która pomoże zabezpieczyć środowisko Service Fabric platformy Azure.

## <a name="introduction"></a>Wprowadzenie
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

## <a name="checklist"></a>Lista kontrolna
Skorzystaj z poniższej listy kontrolnej, aby upewnić się, że nie zawarto żadnych ważnych problemów związanych z zarządzaniem i konfiguracją bezpiecznego rozwiązania Service Fabric platformy Azure.


|Kategoria listy kontrolnej| Opis |
| ------------ | -------- |
|[Kontrola dostępu oparta na rolach (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>Kontrola dostępu pozwala administratorowi klastra ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bezpieczniejszy.</li><li>Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu i zapisu). </li><li> Użytkownicy domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług.</li></ul>|
|[Certyfikaty X. 509 i Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>[Certyfikaty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) używane w klastrach z uruchomionym obciążeniem produkcyjnym powinny być tworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskanej z zatwierdzonego [urzędu certyfikacji (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nigdy nie należy używać żadnych [certyfikatów tymczasowych ani testowych](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) w środowisku produkcyjnym, które są tworzone za pomocą narzędzi, takich jak [Makecert. exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Można użyć certyfikatu z podpisem [własnym](../../service-fabric/service-fabric-windows-cluster-x509-security.md) , ale należy to zrobić tylko w przypadku klastrów testowych, a nie w środowisku produkcyjnym.</li></ul>|
|[Zabezpieczenia klastra](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Scenariusze zabezpieczeń klastra obejmują zabezpieczenia między węzłami, bezpieczeństwo klienta w węźle, [kontrolę dostępu opartą na rolach (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Uwierzytelnianie klastra](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Uwierzytelnia [komunikację między węzłami](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) w ramach Federacji klastra. </li></ul>|
|[Uwierzytelnianie serwera](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Uwierzytelnia [punkty końcowe zarządzania klastrem](../../service-fabric/service-fabric-cluster-creation-via-portal.md) do klienta zarządzania.</li></ul>|
|[Zabezpieczenia aplikacji](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.</li><li>   Szyfrowanie danych między węzłami podczas replikacji.</li></ul>|
|[Certyfikat klastra](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze.</li><li>    Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisk palca i pomocniczy w zmiennych ThumbprintSecondary.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. Można użyć dwóch różnych certyfikatów serwera, podstawowych i pomocniczych do uaktualnienia.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Jest to zestaw certyfikatów, które mają zostać zainstalowane na uwierzytelnionych klientach. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Ustaw nazwę pospolitą pierwszego certyfikatu klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odciskiem palca dla wystawcy tego certyfikatu. </li></ul>|
|ReverseProxyCertificate| <ul><li>Jest to opcjonalny certyfikat, który można określić, jeśli chcesz zabezpieczyć [zwrotny serwer proxy](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Usługa Key Vault| <ul><li>Służy do zarządzania certyfikatami dla klastrów Service Fabric na platformie Azure.  </li></ul>|


## <a name="next-steps"></a>Następne kroki

- [Najlepsze rozwiązania w zakresie zabezpieczeń Service Fabric](service-fabric-best-practices.md)
- [Service Fabric proces uaktualniania klastra i oczekiwania z niego](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Service Fabric wprowadzenie modelu kondycji](../../service-fabric/service-fabric-health-introduction.md).

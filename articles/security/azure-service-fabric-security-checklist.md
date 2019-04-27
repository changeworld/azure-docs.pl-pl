---
title: Usługa Azure service fabric — Lista kontrolna zabezpieczeń | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw Lista kontrolna zabezpieczeń zabezpieczeń sieci szkieletowej platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611541"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista kontrolna zabezpieczeń usługi Azure Service Fabric
Ten artykuł zawiera listę kontrolną "łatwy w użyciu", która pomoże Ci zabezpieczyć środowisku usługi Azure Service Fabric.

## <a name="introduction"></a>Wprowadzenie
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

## <a name="checklist"></a>Lista kontrolna
Poniższa lista kontrolna umożliwia daje pewność, że jeszcze nie pomijane wszelkie istotne problemy w zarządzania i konfiguracji z bezpiecznych rozwiązań usługi Azure Service Fabric.


|Lista kontrolna kategorii| Opis |
| ------------ | -------- |
|[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Kontrola dostępu pozwala administrator klastra ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bardziej bezpieczne.</li><li>Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu/zapisu). </li><li> Użytkownicy, domyślnie mają tylko dostęp do odczytu do możliwości zarządzania (na przykład, zapytanie możliwości) i możliwość usuwania aplikacji i usług.</li></ul>|
|[Certyfikaty X.509 i Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certyfikaty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) używane w klastrach, działających obciążeń produkcyjnych powinny zostać utworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskane z zatwierdzonych [urząd certyfikacji (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nigdy nie używaj żadnego [tymczasowy lub certyfikaty testów](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) w środowisku produkcyjnym, które są tworzone za pomocą narzędzi takich jak [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Możesz użyć [certyfikatu z podpisem własnym](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) jednak tylko to zrobić w przypadku klastrów testowych, a nie w środowisku produkcyjnym.</li></ul>|
|[Zabezpieczenia klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Zabezpieczenia węzła do węzła, węzeł klienta zabezpieczeń, są następujące scenariusze zabezpieczeń klastra [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Uwierzytelnianie klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Uwierzytelnia [komunikacji między węzłami](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) dla Federacji klastra. </li></ul>|
|[Uwierzytelnianie serwera](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Uwierzytelnia [klastra punktów końcowych zarządzania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) kliencie zarządzania.</li></ul>|
|[Zabezpieczenia aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.</li><li>   Szyfrowanie danych między węzłami podczas replikacji.</li></ul>|
|[Certyfikat klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze.</li><li>    Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisku palca, które pomocniczej w zmiennych ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Można użyć dwóch certyfikatów na innym serwerze, podstawowy i pomocniczy dla uaktualnienie.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Jest to zestaw certyfikatów, które chcesz zainstalować na klientach uwierzytelniony. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Nazwa pospolita pierwszy certyfikat klienta należy ustawić dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu. </li></ul>|
|ReverseProxyCertificate| <ul><li>Jest to opcjonalne certyfikat, który może być określona, jeśli chcesz zabezpieczyć swoje [odwrotnego serwera Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Usługa Key Vault| <ul><li>Używany do zarządzania certyfikatami klastrów usługi Service Fabric na platformie Azure.  </li></ul>|


## <a name="next-steps"></a>Kolejne kroki

- [Najlepsze rozwiązania dotyczące zabezpieczeń usługi Service Fabric](azure-service-fabric-security-best-practices.md)
- [Proces uaktualniania klastra usługi Service Fabric i oczekiwania ze strony użytkownika](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Zarządzanie aplikacje usługi Service Fabric w programie Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Wprowadzenie do modelu kondycji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).

---
title: Zalecenia dotyczące zabezpieczeń
description: Zaimplementuj zalecenia dotyczące zabezpieczeń, aby pomóc w wypełnieniu zobowiązań w zakresie bezpieczeństwa określonych w naszym modelu wspólnej odpowiedzialności. Zwiększ bezpieczeństwo aplikacji.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684109"
---
# <a name="security-recommendations-for-app-service"></a>Zalecenia dotyczące zabezpieczeń dla usługi app service

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń usługi Azure App Service. Wdrożenie tych zaleceń pomoże Ci wypełnić swoje obowiązki w zakresie zabezpieczeń opisane w naszym modelu współużytkowania i poprawi ogólne bezpieczeństwo rozwiązań aplikacji sieci Web. Aby uzyskać więcej informacji na temat tego, co firma Microsoft robi, aby wypełnić obowiązki dostawcy usług, przeczytaj artykuł [Zabezpieczenia infrastruktury platformy Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze |
|-|-|----|
| Bądź na bieżąco | Użyj najnowszych wersji obsługiwanych platform, języków programowania, protokołów i struktur. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze |
|-|----|
| Wyłączanie dostępu anonimowego | Jeśli nie musisz obsługiwać żądań anonimowych, wyłącz dostęp anonimowy. Aby uzyskać więcej informacji na temat opcji uwierzytelniania usługi Azure App Service, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md).|
| Wymagaj uwierzytelniania | Jeśli to możliwe, użyj modułu uwierzytelniania usługi App Service zamiast pisania kodu do obsługi uwierzytelniania i autoryzacji. Zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md). |
| Ochrona zasobów zaplecza przy użyciu uwierzytelnionego dostępu | Można użyć tożsamości użytkownika lub użyć tożsamości aplikacji do uwierzytelniania do zasobu zaplecza. Jeśli zdecydujesz się użyć tożsamości aplikacji, użyj [tożsamości zarządzanej](overview-managed-identity.md).
| Wymagaj uwierzytelniania certyfikatu klienta | Uwierzytelnianie certyfikatu klienta zwiększa bezpieczeństwo, zezwalając tylko na połączenia od klientów, którzy mogą uwierzytelniać się przy użyciu certyfikatów, które podasz. |

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze |
|-|-|
| Przekierowywanie protokołu HTTP do usług HTTPs | Domyślnie klienci mogą łączyć się z aplikacjami internetowymi przy użyciu protokołu HTTP lub HTTPS. Zalecamy przekierowanie protokołu HTTP do adresów HTTPs, ponieważ protokół HTTPS używa protokołu SSL/TLS do zapewnienia bezpiecznego połączenia, które jest zarówno szyfrowane, jak i uwierzytelniane. |
| Szyfruj komunikację z zasobami platformy Azure | Gdy aplikacja łączy się z zasobami platformy Azure, takimi jak [baza danych SQL](https://azure.microsoft.com/services/sql-database/) lub usługa Azure [Storage,](/azure/storage/)połączenie pozostaje na platformie Azure. Ponieważ połączenie przechodzi przez sieć współużytkowaną na platformie Azure, należy zawsze szyfrować całą komunikację. |
| Wymagaj najnowszej możliwej wersji TLS | Od 2018 r. nowe aplikacje usługi Azure App Service korzystają z protokołu TLS 1.2. Nowsze wersje protokołu TLS zawierają ulepszenia zabezpieczeń w porównaniu ze starszymi wersjami protokołu. |
| Korzystanie z FTPS | Usługa App Service obsługuje zarówno ftp, jak i FTPS do wdrażania plików. W miarę możliwości należy używać ftps zamiast ftp. Jeśli jeden lub oba z tych protokołów nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps). |
| Zabezpieczanie danych aplikacji | Nie przechowuj wpisów tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API lub klucze prywatne w kodzie lub plikach konfiguracyjnych. Powszechnie akceptowane podejście jest dostęp do nich jako [zmienne środowiskowe](https://wikipedia.org/wiki/Environment_variable) przy użyciu wzorca standardowego w wybranym języku. W usłudze Azure App Service można definiować zmienne środowiskowe za pomocą [ustawień aplikacji](web-sites-configure.md) i [ciągów połączeń](web-sites-configure.md). Ustawienia aplikacji i parametry połączenia są przechowywane zaszyfrowane na platformie Azure. Ustawienia aplikacji są odszyfrowywane tylko przed wstrzyknięciem do pamięci procesu aplikacji po uruchomieniu aplikacji. Klucze szyfrowania są regularnie obracane. Alternatywnie można zintegrować aplikację usługi Azure App Service z [usługą Azure Key Vault](/azure/key-vault/) w celu zaawansowanego zarządzania wpisami tajnymi. Uzyskując [dostęp do usługi Key Vault za pomocą tożsamości zarządzanej,](../key-vault/tutorial-web-application-keyvault.md)aplikacja usługi App Service może bezpiecznie uzyskiwać dostęp do potrzebnych wpisów tajnych. |

## <a name="networking"></a>Obsługa sieci

| Zalecenie | Komentarze |
|-|-|
| Używanie statycznych ograniczeń ip | Usługa Azure App Service w systemie Windows umożliwia zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać indywidualne adresy IP lub zakres adresów IP zdefiniowanych przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [Statyczne ograniczenia adresów IP usługi Azure App Service](app-service-ip-restrictions.md).  |
| Użyj izolowanej warstwy cenowej | Z wyjątkiem warstwy cenowej izolowane wszystkie warstwy uruchomić aplikacje w infrastrukturze sieci udostępnionej w usłudze Azure App Service. Warstwa izolowana zapewnia pełną izolację sieci, uruchamiając aplikacje w dedykowanym [środowisku usługi app service.](environment/intro.md) Środowisko usługi aplikacji działa we własnym wystąpieniu [usługi Azure Virtual Network](/azure/virtual-network/).|
| Korzystanie z bezpiecznych połączeń podczas uzyskiwania dostępu do zasobów lokalnych | Można użyć [połączeń hybrydowych,](app-service-hybrid-connections.md) [integracji sieci wirtualnej](web-sites-integrate-with-vnet.md)lub [środowiska usługi app service](environment/intro.md) do łączenia się z zasobami lokalnymi. |
| Ograniczanie narażenia na przychodzący ruch sieciowy | Sieciowe grupy zabezpieczeń umożliwiają ograniczenie dostępu do sieci i kontrolowanie liczby ujawnionych punktów końcowych. Aby uzyskać więcej informacji, zobacz [Jak kontrolować ruch przychodzący do środowiska usługi app service](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze |
|-|-|
|Korzystanie z warstwy standardowej usługi Azure Security Center | [Usługa Azure Security Center](../security-center/security-center-app-services.md) jest natywnie zintegrowana z usługą Azure App Service. Może przeprowadzać oceny i dostarczać zalecenia dotyczące zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz [Bezpieczna dokumentacja programisty .](../security/fundamentals/abstract-develop-secure-apps.md)

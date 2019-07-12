---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure App Service
description: Zalecenia dotyczące zabezpieczeń dla usługi Azure App Service. Wdrażanie tych zaleceń dotyczących pomocy, które spełniają Twoje obowiązki zabezpieczeń, zgodnie z opisem w naszym modelu odpowiedzialności i będzie poprawi ogólnego stanu zabezpieczeń dla rozwiązań aplikacji sieci web.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718254"
---
# <a name="security-recommendations-for-app-service"></a>Zalecenia dotyczące zabezpieczeń dla usługi App Service

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla usługi Azure App Service. Wdrażanie tych zaleceń dotyczących pomocy, które spełniają Twoje obowiązki zabezpieczeń, zgodnie z opisem w naszym modelu odpowiedzialności i będzie poprawi ogólnego stanu zabezpieczeń dla rozwiązań aplikacji sieci Web. Dla jest więcej informacji na temat jak Microsoft do spełnienia obowiązki dostawcy usług, przeczytaj [bezpieczeństwa infrastruktury platformy Azure](../security/azure-security-infrastructure.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze |
|-|-|----|
| Najnowsze informacje | Użyj najnowszej wersji obsługiwanych platform, języków programowania, protokołów i struktur. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze |
|-|----|
| Wyłączanie dostępu anonimowego | Jeśli nie potrzebujesz do obsługi żądań anonimowych, wyłącz dostęp anonimowy. Aby uzyskać więcej informacji na temat opcji uwierzytelniania w usłudze Azure App Service, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md).|
| Wymagaj uwierzytelniania | Zawsze, gdy jest to możliwe, należy użyć modułu uwierzytelniania usługi App Service zamiast pisania kodu do obsługi uwierzytelniania i autoryzacji. Zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md). |
| Ochrona zasobów zaplecza przy użyciu dostęp uwierzytelniony | Możesz używać tożsamości użytkownika lub używania tożsamości aplikacji w celu uwierzytelniania zasobów zaplecza. Jeśli zdecydujesz się użyć tożsamości aplikacji [tożsamości zarządzanej](overview-managed-identity.md).
| Wymagaj uwierzytelniania certyfikatu klienta | Uwierzytelnianie certyfikatu klienta zwiększa bezpieczeństwo, zezwalając tylko połączenia od klientów, którzy mogą uwierzytelniać za pomocą certyfikatów, które należy podać. |

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze |
|-|-|
| Przekierowywanie protokołu HTTP do HTTPs | Domyślnie klienci mogą łączyć się z aplikacji sieci web za pomocą zarówno protokołu HTTP lub HTTPS. Firma Microsoft zaleca przekierowywanie HTTP do HTTPs, ponieważ protokół HTTPS korzysta z protokołu SSL/TLS w celu zapewnienia bezpiecznego połączenia jest zaszyfrowany i uwierzytelniony. |
| Szyfrowanie komunikacji z zasobów platformy Azure | Gdy aplikacja łączy się z zasobami platformy Azure, takich jak [bazy danych SQL](https://azure.microsoft.com/services/sql-database/) lub [usługi Azure Storage](/azure/storage/), połączenie pozostaje na platformie Azure. Ponieważ połączenia odbywa się przez udostępnionej sieci na platformie Azure, należy zawsze Szyfruj całej komunikacji. |
| Wymaga najnowszej wersji protokołu TLS | Ponieważ 2018 nowe aplikacje usługi Azure App Service używa protokołu TLS 1.2. Nowsze wersje protokołu TLS obejmują udoskonalenia w zakresie zabezpieczeń przez starsze wersje protokołu. |
| Użyj protokołu FTPS | Usługa App Service obsługuje zarówno protokołu FTP i FTPS wdrażania plików. Zamiast protokołu FTP, gdy jest to możliwe, należy użyć protokołu FTPS. Jeśli co najmniej jeden z tych protokołów nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps). |
| Zabezpieczanie danych aplikacji | Nie należy przechowywać kluczy tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API lub klucze prywatne w plikach kodu lub konfiguracji. Powszechnie akceptowane podejściem jest dostęp do nich jako [zmienne środowiskowe](https://wikipedia.org/wiki/Environment_variable) przy użyciu standardowego wzorca w wybranym języku. W usłudze Azure App Service, można zdefiniować zmienne środowiskowe za pośrednictwem [ustawienia aplikacji](web-sites-configure.md) i [parametry połączenia](web-sites-configure.md). Ustawienia aplikacji i parametry połączenia są przechowywane w postaci zaszyfrowanej na platformie Azure. Ustawienia aplikacji są odszyfrowywane, tylko przed są wstrzykiwane do pamięci procesu aplikacji, po uruchomieniu aplikacji. Klucze szyfrowania są obracane regularnie. Alternatywnie można zintegrować aplikację usługi Azure App Service za pomocą [usługi Azure Key Vault](/azure/key-vault/) zarządzania zaawansowane wpisów tajnych. Przez [uzyskiwania dostępu do usługi Key Vault za pomocą tożsamości zarządzanej](../key-vault/tutorial-web-application-keyvault.md), aplikacji usługi app Service bezpieczny dostęp do danych poufnych, potrzebujesz. |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze |
|-|-|
| Użyj ograniczeń statycznych adresów IP | Usługa Azure App Service na Windows pozwala zdefiniować listę adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać pojedyncze adresy IP lub zakres adresów IP określone przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [usługi Azure App Service statyczne ograniczenia adresów IP](app-service-ip-restrictions.md).  |
| Użyj izolowane warstwy cenowej | Z wyjątkiem izolowane warstwy cenowej dla wszystkich warstw uruchamiaj swoje aplikacje w udostępnionej infrastrukturze sieciowej w usłudze Azure App Service. Izolowane warstwy zapewnia izolację sieci pełną, uruchamiając swoje aplikacje w dedykowanej [środowiska App Service environment](environment/intro.md). Środowiska usługi App Service uruchamia wystąpienia programu [Azure Virtual Network](/azure/virtual-network/).|
| Użyj bezpiecznych połączeń podczas uzyskiwania dostępu do zasobów lokalnych | Możesz użyć [połączeń hybrydowych](app-service-hybrid-connections.md), [Integracja sieci wirtualnej](web-sites-integrate-with-vnet.md), lub [środowiska App Service environment](environment/intro.md) połączyć się z zasobami lokalnymi. |
| Ograniczenia narażenia na przychodzący ruch sieciowy | Sieciowe grupy zabezpieczeń umożliwiają ograniczenie dostępu przez sieć i kontrolować liczbę udostępniane punkty końcowe. Aby uzyskać więcej informacji, zobacz [jak do sterowania ruchem przychodzącym do środowiska usługi App Service](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze |
|-|-|
|Usługa Azure Security Center w warstwie standardowa | [Usługa Azure Security Center](../security-center/security-center-app-services.md) natywnej integracji z usługą Azure App Service. Można uruchomić oceny i przedstawić zalecenia w zakresie zabezpieczeń. |

## <a name="next-steps"></a>Kolejne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz [bezpiecznego dokumentacji dla deweloperów](../security/abstract-develop-secure-apps.md).

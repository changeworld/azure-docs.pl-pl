---
title: Omówienie usługi Azure Key Vault | Microsoft Docs
description: Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: aae7836448ff27b4c80d7bb53e108034ee52db1c
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586295"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:
- **Zarządzanie wpisami tajnymi** — usługa Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami** — usługa Azure Key Vault może być także używana jako rozwiązanie do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- **Zarządzanie certyfikatami** — usługa Azure Key Vault pozwala również łatwo aprowizować i wdrażać prywatne i publiczne certyfikaty Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użycia z platformą Azure i połączonymi zasobami wewnętrznymi oraz zarządzać tymi certyfikatami. 
- **Wpisy tajne w magazynie wspierane przez moduły HSM (Hardware Security Module)** — wpisy tajne i klucze mogą być chronione przez oprogramowanie lub moduły HSM zweryfikowane pod kątem zgodności ze standardem FIPS 140-2 poziom 2.

## <a name="why-use-azure-key-vault"></a>Jakie są zalety korzystania z usługi Azure Key Vault?

### <a name="centralize-application-secrets"></a>Scentralizowana obsługa wpisów tajnych aplikacji

Centralny magazyn wpisów tajnych aplikacji w usłudze Azure Key Vault umożliwia kontrolowanie ich dystrybucji. Usługa Key Vault znacznie ogranicza prawdopodobieństwo przypadkowego ujawnienia wpisów tajnych. Korzystając z usługi Key Vault, deweloperzy aplikacji nie muszą już przechowywać informacji zabezpieczeń w aplikacji. Konieczność umieszczania ich w kodzie zostaje wyeliminowana. Na przykład aplikacja może potrzebować połączenia z bazą danych. Zamiast przechowywać parametry połączenia w kodzie aplikacji, możesz zapisać je bezpiecznie w usłudze Key Vault.

Aplikacje mogą uzyskiwać dostęp do potrzebnych informacji w bezpieczny sposób za pomocą identyfikatorów URI, które pozwalają na pobranie określonych wersji wpisu tajnego po umieszczeniu klucza lub wpisu tajnego aplikacji w usłudze Azure Key Vault. Jest to możliwe bez konieczności pisania niestandardowego kodu w celu ochrony poufnych informacji.

### <a name="securely-store-secrets-and-keys"></a>Bezpieczne przechowywanie wpisów tajnych i kluczy

Wpisy tajne i klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM). Używane moduły HSM zostały zweryfikowane pod kątem zgodności ze standardem Federal Information Processing Standards (FIPS) 140-2 Level 2.

Udzielenie dostępu do magazynu kluczy wywołującemu użytkownikowi lub aplikacji wymaga odpowiedniego uwierzytelnienia i autoryzacji. Uwierzytelnianie ustala tożsamość elementu wywołującego, a autoryzacja określa, jakie operacje może on wykonywać.

Uwierzytelnianie jest wykonywane za pośrednictwem usługi Azure Active Directory. Autoryzacja może odbywać się przy użyciu funkcji kontroli dostępu na podstawie ról (RBAC, role-based access control) lub zasad dostępu usługi Key Vault. Funkcja RBAC jest używana do obsługi zarządzania magazynami, a zasady dostępu do magazynu kluczy są używane podczas próby uzyskania dostępu do danych przechowywanych w magazynie.

Usługa Azure Key Vault może być chroniona za pomocą programowego lub sprzętowego modułu HSM. W przypadku sytuacji wymagających dodatkowych zabezpieczeń możesz zaimportować klucz do modułu HSM lub wygenerować w nim klucze, które nigdy nie opuszczają modułu HSM. Firma Microsoft używa sprzętowych modułów zabezpieczeń firmy Thales. Aby przenieść klucz z modułu HSM do usługi Azure Key Vault, można użyć narzędzi firmy Thales.

Usługa Azure Key Vault została zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje dane ani nie mogła ich wyodrębnić.

### <a name="monitor-access-and-use"></a>Monitorowanie dostępu i użycia

Po utworzeniu kilku magazynów usługi Key Vault może zajść potrzeba monitorowania sposobu oraz czasu dostępu do kluczy i wpisów tajnych. Jest to możliwe przez włączenie rejestrowania dla usługi Key Vault. Usługę Azure Key Vault można skonfigurować w następujących celach:

- Archiwizowanie na koncie magazynu.
- Przesyłanie strumieniowe do centrum zdarzeń.
- Wysyłanie dzienników do usługi Log Analytics.

Kontrolujesz dzienniki i możesz je zabezpieczyć przez ograniczenie dostępu, a także możesz usunąć dzienniki, które nie są już potrzebne.

### <a name="simplified-administration-of-application-secrets"></a>Uproszczone administrowanie wpisami tajnymi aplikacji

W przypadku przechowywania cennych danych należy wykonać kilka czynności. Informacje zabezpieczające muszą być bezpieczne, podlegać procesom cyklu życia i być łatwo dostępne. Usługa Azure Key Vault w znacznym stopniu to ułatwia, na następujące sposoby:

- Brak konieczności posiadania wiedzy z zakresu sprzętowych modułów zabezpieczeń w firmie.
- Skalowanie w górę w krótkim czasie w celu spełnienia nagłego zapotrzebowania organizacji.
- Replikowanie zawartości usługi Key Vault w regionie do regionu pomocniczego. Zapewnia to wysoką dostępność i eliminuje konieczność wykonywania jakichkolwiek czynności przez administratora w celu wyzwolenia trybu failover.
- Udostępnianie standardowych opcji administrowania platformą Azure za pośrednictwem portalu, interfejsu wiersza polecenia platformy Azure i programu PowerShell.
- Zautomatyzowanie pewnych zadań związanych z certyfikatami kupowanymi od publicznych urzędów certyfikacji, na przykład ich rejestracji i odnawiania.

Usługa Azure Key Vault umożliwia także rozdzielenie wpisów tajnych aplikacji. Aplikacje mają dostęp tylko do magazynu, do którego zezwolono im na dostęp, i są ograniczone do wykonywania tylko określonych operacji. Istnieje możliwość utworzenia usługi Azure Key Vault dla aplikacji i ograniczenia użycia wpisów tajnych przechowywanych w usłudze Key Vault do konkretnej aplikacji i konkretnego zespołu deweloperów.

### <a name="integrate-with-other-azure-services"></a>Integracja z innymi usługami platformy Azure

Usługi Key Vault, zabezpieczonego magazynu na platformie Azure, użyto do uproszczenia scenariuszy obejmujących [usługę Azure Disk Encryption](../security/azure-security-disk-encryption.md), [funkcję Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) programu SQL Server i usługi Azure SQL Database oraz [usługę Azure Web Apps]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). Samą usługę Key Vault można zintegrować z kontami magazynu, centrami zdarzeń i usługą Log Analytics.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: tworzenie usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia](quick-create-cli.md)
- [Konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault](tutorial-web-application-keyvault.md)

---
title: Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta
description: Azure Database for PostgreSQL szyfrowanie danych za pomocą jednego serwera z kluczem zarządzanym przez klienta umożliwia Bring Your Own Key (BYOK) na potrzeby ochrony danych w czasie spoczynku oraz pozwala organizacjom wdrożyć Rozdzielenie obowiązków w zakresie zarządzania kluczami i danymi.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 263fdda178752ee22997a03a11902a7bff4791dc
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028617"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z AskAzureDBforPostgreSQL@service.microsoft.com.

Azure Database for PostgreSQL szyfrowanie danych za pomocą jednego serwera z kluczem zarządzanym przez klienta umożliwia Bring Your Own Key (BYOK) na potrzeby ochrony danych w czasie spoczynku oraz pozwala organizacjom wdrożyć Rozdzielenie obowiązków w zakresie zarządzania kluczami i danymi. W przypadku szyfrowania zarządzanego przez klienta użytkownik jest odpowiedzialny za i w pełnej kontroli cyklu życia klucza (tworzenia kluczy, przekazywania, rotacji, usuwania), uprawnień do użycia kluczy i inspekcji operacji na kluczach.

W przypadku Azure Database for PostgreSQL jednego serwera szyfrowanie danych jest ustawiane na poziomie serwera. W przypadku tej formy szyfrowania danych klucz jest używany do szyfrowania klucza szyfrowania bazy danych (.), który jest kluczem asymetryczny zarządzanym przez klienta przechowywanym w Azure Key Vaultm przez klienta i zarządzanym przez klienta [(AKV)](../key-vault/key-Vault-secure-your-key-Vault.md), opartym na chmurze zewnętrznym systemem zarządzania kluczami. AKV jest wysoce dostępny i udostępnia skalowalny bezpieczny magazyn dla kluczy kryptograficznych RSA, opcjonalnie obsługiwane w przypadku FIPS 140-2 Level 2 sprawdzone sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania/odszyfrowywania przy użyciu klucza do autoryzowanych jednostek. Klucz może być wygenerowany przez Key Vault, zaimportowano lub [przetransferowany do Key Vault z urządzenia modułu HSM Premium](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for PostgreSQL jeden serwer obsługuje warstwy cenowe Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="benefits"></a>Korzyści

Szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera zapewnia następujące korzyści:

* Zwiększona przejrzystość i szczegółowa kontrola i zarządzanie kluczem szyfrowania 
* Centralne zarządzanie i organizacja kluczy, udostępniając je w Azure Key Vault. 
* Możliwość wdrożenia separacji obowiązków w zakresie zarządzania kluczami i danymi w organizacji
* Zarządzanie kluczami za pomocą zarządzania danymi w organizacji, dzięki czemu administrator Key Vault może odwołać uprawnienia dostępu do klucza, aby zaszyfrowana baza danych była niedostępna 
* Większe zaufanie od klientów końcowych, ponieważ Azure Key Vault zaprojektowano w taki sposób, aby firma Microsoft nie widziała ani nie wyodrębni kluczy szyfrowania

## <a name="terminology-and-description"></a>Terminologia i opis

**Klucz szyfrowania danych (undek)** — symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza sprawia, że ataki analizy kryptograficznej są trudniejsze. Dostęp do DEKs jest wymagany przez dostawcę zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok. Gdy klucz szyfrowania danych jest zastępowany nowym kluczem, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.

**Klucz szyfrowania klucza (KEK)** — klucz szyfrowania służący do szyfrowania kluczy szyfrowania danych. Użycie klucza szyfrowania klucza, który nigdy nie pozostawia Key Vault, pozwala na szyfrowanie kluczy szyfrowania danych i sterowanie nimi. Jednostka, która ma dostęp do elementu KEK, może być różna od jednostki, która wymaga tego elementu. Ponieważ KEK jest wymagany do odszyfrowania DEKs, KEK jest efektywnie jednym punktem, przez który DEKs może być skutecznie usunięty przez usunięcie KEK.

Klucze szyfrowania danych, zaszyfrowane przy użyciu kluczy szyfrowania kluczy, są przechowywane oddzielnie i tylko jednostka mająca dostęp do klucza szyfrowania klucza może odszyfrować te klucze szyfrowania danych. Aby uzyskać więcej informacji, zobacz [zabezpieczenia w szyfrowaniu](../security/fundamentals/encryption-atrest.md)w stanie spoczynku.

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Jak działa szyfrowanie danych z kluczem zarządzanym przez klienta

![Korzystanie z własnego klucza — przegląd](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Aby serwer PostgreSQL mógł używać kluczy zarządzanych przez klienta przechowywanych w usłudze AKV do szyfrowania danych w programie, administrator Key Vault musi udzielić następujących praw dostępu serwerowi przy użyciu unikatowej tożsamości:

* **Get** -do pobierania publicznej części i właściwości klucza w Key Vault
* **wrapKey** — aby można było szyfrować klucz szyfrowania danych
* **unwrapKey** — aby można było odszyfrować klucz szyfrowania danych

Key Vault administrator może również [włączyć rejestrowanie zdarzeń inspekcji Key Vault](../azure-monitor/insights/azure-key-vault.md), aby mogły być później poddane inspekcji.

Gdy serwer jest skonfigurowany do korzystania z klucza zarządzanego przez klienta, który jest przechowywany w Key Vault, serwer wysyła do Key Vault do szyfrowania szyfrowanie danych. Key Vault zwraca zaszyfrowany klucz szyfrowania danych, który jest przechowywany w bazie. Analogicznie, jeśli jest to konieczne, serwer wysyła chronione szyfrowanie szyfrowania danych do Key Vault na potrzeby odszyfrowywania. Audytorzy mogą używać Azure Monitor do przeglądania dzienników AuditEvent Key Vault, jeśli rejestrowanie jest włączone.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Wymagania dotyczące konfigurowania szyfrowania danych dla Azure Database for PostgreSQL pojedynczego serwera

### <a name="requirements-for-configuring-akv"></a>Wymagania dotyczące konfigurowania AKV

* Key Vault i Azure Database for PostgreSQL pojedynczy serwer musi należeć do tej samej dzierżawy Azure Active Directory (AAD). Key Vault i interakcje między dzierżawcami nie są obsługiwane. Późniejsze przeniesienie zasobów wymaga ponownego skonfigurowania szyfrowania danych. Dowiedz się więcej o przenoszeniu zasobów.
* Funkcja usuwania nietrwałego musi być włączona na Key Vault, aby chronić przed usunięciem klucza przypadkowej utraty danych (lub Key Vault). Zasoby usunięte nietrwale są przechowywane przez 90 dni, chyba że w międzyczasie zostanie odzyskany lub usunięty przez klienta. Akcje odzyskania i przeczyszczania mają własne uprawnienia skojarzone z zasadami dostępu Key Vault. Funkcja usuwania nietrwałego jest domyślnie wyłączona i można ją włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia. Nie można jej włączyć za pośrednictwem Azure Portal.
* Przyznaj Azure Database for PostgreSQL dostęp do Key Vault za pomocą uprawnień **Get, wrapKey, unwrapKey** przy użyciu unikatowej tożsamości zarządzanej. W przypadku korzystania z Azure Portal unikatowa identyfikacja jest automatycznie tworzona, gdy szyfrowanie danych jest włączone na pojedynczym serwerze PostgreSQL. Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące korzystania z Azure Portal, zobacz [Konfigurowanie szyfrowania danych dla pojedynczego serwera PostgreSQL](howto-data-encryption-portal.md) .

* W przypadku korzystania z zapory z AKV należy włączyć opcję *Zezwalaj zaufanym usługom firmy Microsoft na pominięcie zapory*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Wymagania dotyczące konfigurowania klucza zarządzanego przez klienta

* Klucz zarządzany przez klienta, który ma być używany na potrzeby szyfrowania, może być tylko asymetryczny, RSA 2028.
* Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być przyszłą datą i godziną.
* Klucz musi być w stanie *włączony* .
* W przypadku importowania istniejącego klucza do Key Vault upewnij się, że jest on udostępniany w obsługiwanych formatach plików (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Zalecenia dotyczące korzystania z szyfrowania danych przy użyciu klucza zarządzanego przez klienta

### <a name="recommendation-for-configuring-akv"></a>Zalecenie dotyczące konfigurowania AKV

* Ustaw blokadę zasobów na Key Vault, aby określić, kto może usunąć ten krytyczny zasób i zapobiegać przypadkowemu lub nieautoryzowanemu usunięciu. Dowiedz się więcej o blokadach zasobów.
* Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania: Key Vault zawiera dzienniki, które można łatwo wstrzyknąć do innych informacji o zabezpieczeniach i narzędzi do zarządzania zdarzeniami. Azure Monitor Log Analytics to jeden przykład usługi, która jest już zintegrowana.

* Upewnij się, że Key Vault i Azure Database for PostgreSQL pojedynczy serwer znajdują się w tym samym regionie, aby zapewnić szybszy dostęp do operacji zawijania/odpakowania danych.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Zalecenie dotyczące konfigurowania klucza zarządzanego przez klienta

* Przechowuj kopię klucza zarządzanego przez klienta (KEK) w bezpiecznym miejscu lub wykorzystaj ją w usłudze Escrow.

* Jeśli klucz jest generowany w Key Vault, Utwórz kopię zapasową klucza przed użyciem klucza w AKV po raz pierwszy. Kopię zapasową można przywrócić tylko do Azure Key Vault. Dowiedz się więcej na temat polecenia [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) .

## <a name="inaccessible-customer-managed-key-condition"></a>Niedostępny warunek klucza zarządzanego przez klienta

W przypadku skonfigurowania szyfrowania danych z kluczem zarządzanym przez klienta w Azure Key Vault (AKV), aby serwer mógł pozostać w trybie online, wymagany jest ciągły dostęp do tego klucza. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w programie AKV, w ciągu 10 minut serwer zacznie odrzucać wszystkie połączenia z odpowiednim komunikatem o błędzie i zmienić stan serwera na **niedostępny**. Jedyną akcją, którą można wykonać w bazie danych w stanie niedostępnym, jest usunięcie jej.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Przypadkowe odwoływanie dostępu do klucza z Azure Key Vault (AKV)

Może się zdarzyć, że ktoś z wystarczającymi prawami dostępu do Key Vault przypadkowo wyłącza dostęp serwera do klucza przez:

* Odwoływanie uprawnień Key Vault Get, wrapKey, unwrapKey z serwera
* Usuwanie klucza
* Usuwanie Key Vault
* zmiana reguł zapory Key Vault

* Usuwanie tożsamości zarządzanej serwera w Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Monitorowanie klucza zarządzanego przez klienta w Key Vault

Aby monitorować stan bazy danych i włączyć alert o utracie dostępu do funkcji ochrony TDE, skonfiguruj następujące funkcje platformy Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md) — niedostępna baza danych, która utraciła dostęp do klucza klienta, będzie wyświetlana jako "niedostępna" po odmowie pierwszego połączenia z bazą danych.
* [Dziennik aktywności](../service-health/alerts-activity-log-service-notifications.md) — Jeśli dostęp do klucza klienta w Key Vault zarządzanym przez klienta nie powiedzie się, wpisy zostaną dodane do dziennika aktywności. Utworzenie alertów dla tych zdarzeń umożliwi przywrócenie dostępu tak szybko, jak to możliwe.

* [Grupy akcji](../azure-monitor/platform/action-groups.md) można definiować w celu wysyłania powiadomień i alertów na podstawie preferencji, na przykład wiadomości E-mail/SMS/wypychania/głosu, aplikacji logiki, elementu webhook, narzędzia ITSM lub elementu Runbook usługi Automation.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Przywracanie i replika z kluczem zarządzanym klienta w Key Vault

Gdy Azure Database for PostgreSQL jeden serwer jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, każda nowo utworzona kopia serwera (za pomocą operacji przywracania lokalnego lub w trybie geograficznym lub za pośrednictwem replik odczytu) również zostanie zaszyfrowana z tą samą klucz zarządzany przez klienta. Można je jednak zmienić w taki sposób, aby odzwierciedlały klucz zarządzany nowego klienta na potrzeby szyfrowania. Gdy klucz zarządzany przez klienta zostanie zmieniony, stare kopie zapasowe serwera rozpocznie się przy użyciu najnowszego klucza.

Aby uniknąć problemów podczas ustanawiania konfiguracji szyfrowania danych zarządzanych przez klienta podczas przywracania lub tworzenia repliki odczytu, należy wykonać następujące kroki na serwerze głównym i przywracaniu/serwer repliki:

* Zainicjuj proces przywracania lub odczytywania repliki z głównego Azure Database for PostgreSQL jednego serwera.
* Nowo utworzony serwer (przywrócony/Replica) jest przechowywany w stanie niedostępnym, ponieważ jego unikatowa tożsamość nie otrzymała jeszcze uprawnień do Azure Key Vault (AKV)
* Na serwerze przywróconym/repliki ponownie sprawdź poprawność klucza zarządzanego przez klienta w ustawieniach szyfrowania danych, aby upewnić się, że nowo utworzony serwer ma nadane uprawnienia zawijania/odwinięcia do klucza przechowywanego w AKV.

* W celu zapewnienia, że szyfrowanie danych jest zachowywane na serwerze głównym oraz przywracane/replikowane, należy wykonać obie powyższe czynności.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak skonfigurować szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla serwera usługi Azure Database for PostgreSQL przy użyciu Azure Portal](howto-data-encryption-portal.md).

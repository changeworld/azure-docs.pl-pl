---
title: Szyfrowanie danych za pomocą klucza zarządzanego przez klienta — usługa Azure Database for MySQL
description: Usługa Azure Database for MySQL data encryption with a customer-managed key umożliwia wprowadzenie własnego klucza (BYOK) w celu ochrony danych w spoczynku. Umożliwia również organizacjom wdrożenie rozdzielenia obowiązków w zarządzaniu kluczami i danymi.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299128"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Usługa Azure Database dla szyfrowania danych MySQL z kluczem zarządzanym przez klienta

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z . AskAzureDBforMySQL@service.microsoft.com

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta dla usługi Azure Database for MySQL umożliwia przynosze własny klucz (BYOK) w celu ochrony danych w spoczynku. Umożliwia również organizacjom wdrożenie rozdzielenia obowiązków w zarządzaniu kluczami i danymi. Dzięki szyfrowaniu zarządzanej przez klienta użytkownik jest odpowiedzialny za cykl życia klucza, uprawnienia do użycia klucza i inspekcję operacji na kluczach i pod pełną kontrolą.

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta dla usługi Azure Database for MySQL jest ustawione na poziomie serwera. W przypadku danego serwera klucz zarządzany przez klienta, nazywany kluczem szyfrowania klucza (KEK), jest używany do szyfrowania klucza szyfrowania danych (DEK) używanego przez usługę. KEK jest kluczem asymetrycznym przechowywanym w wystąpieniu [usługi Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) należącym do klienta i zarządzanym przez klienta. Klucz szyfrowania klucza (KEK) i klucz szyfrowania danych (DEK) jest opisany bardziej szczegółowo w dalszej części tego artykułu.

Key Vault to oparty na chmurze, zewnętrzny system zarządzania kluczami. Jest wysoce dostępny i zapewnia skalowalną, bezpieczną pamięć masową dla kluczy kryptograficznych RSA, opcjonalnie wspieranych przez sprawdzone moduły zabezpieczeń sprzętu (HSM) fips 140-2 Level 2. Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania i odszyfrowywania autoryzowanym jednostkom. Usługa Key Vault może wygenerować klucz, zaimportować go lub [przenieść z lokalnego urządzenia HSM.](../key-vault/key-Vault-hsm-protected-keys.md)

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database for MySQL obsługuje warstwy cenowe "Ogólnego przeznaczenia" i "Zoptymalizowane pod kątem pamięci".

## <a name="benefits"></a>Korzyści

Szyfrowanie danych dla usługi Azure Database for MySQL zapewnia następujące korzyści:

* Dostęp do danych jest w pełni kontrolowany przez możliwość usunięcia klucza i uczynienie bazy danych niedostępną 
* Pełna kontrola nad cyklem życia klucza, w tym rotacja klucza w celu dostosowania go do zasad korporacyjnych
* Centralne zarządzanie kluczami i organizacja kluczy w usłudze Azure Key Vault
* Możliwość wykonywania rozdzielenia obowiązków między funkcjonariuszami ochrony a DBA i administratorami systemu


## <a name="terminology-and-description"></a>Terminologia i opis

**Klucz szyfrowania danych (DEK)**: Symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych. Szyfrowanie każdego bloku danych za pomocą innego klucza utrudnia ataki analizy kryptograficznej. Dostęp do deks jest potrzebny przez dostawcę zasobów lub wystąpienie aplikacji, który szyfruje i odszyfrowuje określony blok. Po zastąpieniu pliku DEK nowym kluczem tylko dane w skojarzonym z nim bloku muszą być ponownie zaszyfrowane za pomocą nowego klucza.

**Klucz szyfrowania klucza (KEK)**: Klucz szyfrowania używany do szyfrowania plików DEK. KEK, który nigdy nie opuszcza Key Vault pozwala deks się być szyfrowane i kontrolowane. Jednostka, która ma dostęp do KEK może być inna niż jednostka, która wymaga DEK. Ponieważ KEK jest wymagane do odszyfrowania DEKs, KEK jest skutecznie pojedynczy punkt, przez który DEKs mogą być skutecznie usunięte przez usunięcie KEK.

Deks, szyfrowane za pomocą KEKs, są przechowywane oddzielnie. Tylko jednostka z dostępem do KEK może odszyfrować te deks. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia w szyfrowaniu w spoczynku](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Jak działa szyfrowanie danych za pomocą klucza zarządzanego przez klienta

![Diagram przedstawiający omówienie programu Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Aby serwer MySQL używał kluczy zarządzanych przez klienta przechowywanych w uchoniem kluczy do szyfrowania usługi DEK, administrator usługi Key Vault przyznaje serwerowi następujące prawa dostępu:

* **get**: Do pobierania części publicznej i właściwości klucza w magazynie kluczy.
* **wrapKey:** Aby móc zaszyfrować DEK.
* **unwrapKey**: Aby móc odszyfrować DEK.

Administrator magazynu kluczy może również [włączyć rejestrowanie zdarzeń inspekcji usługi Key Vault,](../azure-monitor/insights/azure-key-vault.md)dzięki czemu można je później poddać inspekcji.

Gdy serwer jest skonfigurowany do używania klucza zarządzanego przez klienta przechowywanego w magazynie kluczy, serwer wysyła plik DEK do magazynu kluczy w celu szyfrowania. Usługa Key Vault zwraca zaszyfrowany kod DEK, który jest przechowywany w bazie danych użytkowników. Podobnie, w razie potrzeby serwer wysyła chronionyDEK do magazynu kluczy w celu odszyfrowania. Audytorzy mogą używać usługi Azure Monitor do przeglądania dzienników zdarzeń inspekcji usługi Key Vault, jeśli rejestrowanie jest włączone.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Wymagania dotyczące konfigurowania szyfrowania danych dla usługi Azure Database dla mysql

Następujące wymagania dotyczące konfigurowania usługi Key Vault:

* Usługa Key Vault i Usługa Azure Database for MySQL muszą należeć do tej samej dzierżawy usługi Azure Active Directory (Azure AD). Interakcja między dzierżawcami usługi Key Vault i serwer nie są obsługiwane. Przenoszenie zasobów wymaga ponownego skonfigurowania szyfrowania danych.
* Należy włączyć funkcję usuwania nietrwałego w magazynie kluczy, aby chronić przed utratą danych, jeśli nastąpi przypadkowe usunięcie klucza (lub magazynu kluczy). Nietrwałe usunięte zasoby są zachowywane przez 90 dni, chyba że użytkownik odzyskuje lub czyści je w międzyczasie. Akcje odzyskiwania i przeczyszczania mają własne uprawnienia skojarzone z zasadami dostępu usługi Key Vault. Funkcja usuwania nietrwałego jest domyślnie wyłączona, ale można ją włączyć za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure (należy pamiętać, że nie można jej włączyć za pośrednictwem witryny Azure portal).
* Udziel usługi Azure Database dla MySQL dostęp do magazynu kluczy z get, wrapKey i unwrapKey uprawnień przy użyciu jego unikatowej tożsamości zarządzanej. W witrynie Azure portal unikatowa tożsamość jest automatycznie tworzona, gdy szyfrowanie danych jest włączone na MySQL. Zobacz [Konfigurowanie szyfrowania danych dla MySQL,](howto-data-encryption-portal.md) aby uzyskać szczegółowe instrukcje krok po kroku podczas korzystania z witryny Azure portal.

* W przypadku korzystania z zapory z usługą Key Vault należy włączyć opcję **Zezwalaj zaufanym usługom firmy Microsoft na ominięcie zapory**.

Poniżej przedstawiono wymagania dotyczące konfigurowania klucza zarządzanego przez klienta:

* Klucz zarządzany przez klienta, który ma być używany do szyfrowania DEK może być tylko asymetryczny, RSA 2028.
* Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być datą i godziną przyszłą.
* Klucz musi być w stanie *Włączone.*
* Jeśli importujesz istniejący klucz do magazynu kluczy, upewnij się, że jest`.pfx`on `.byok` `.backup`podtrzymywał w obsługiwanych formatach plików ( , , ).

## <a name="recommendations"></a>Zalecenia

W przypadku korzystania z szyfrowania danych przy użyciu klucza zarządzanego przez klienta oto zalecenia dotyczące konfigurowania usługi Key Vault:

* Ustaw blokadę zasobów w Magazynie kluczy, aby kontrolować, kto może usunąć ten krytyczny zasób i zapobiec przypadkowemu lub nieautoryzowanemu usunięciu.
* Włącz inspekcję i raportowanie wszystkich kluczy szyfrowania. Usługa Key Vault udostępnia dzienniki, które można łatwo wstrzyknąć do innych narzędzi do zarządzania informacjami zabezpieczającymi i zdarzeniami. Usługa Azure Monitor Log Analytics jest jednym z przykładów usługi, która jest już zintegrowana.

* Upewnij się, że usługa Key Vault i usługa Azure Database for MySQL znajdują się w tym samym regionie, aby zapewnić szybszy dostęp do operacji zawijania i odwijania DEK.

Poniżej przedstawiono zalecenia dotyczące konfigurowania klucza zarządzanego przez klienta:

* Zachowaj kopię klucza zarządzanego przez klienta w bezpiecznym miejscu lub escrow go do usługi escrow.

* Jeśli magazyn kluczy generuje klucz, utwórz kopię zapasową klucza przed użyciem klucza po raz pierwszy. Kopię zapasową można przywrócić tylko do magazynu kluczy. Aby uzyskać więcej informacji na temat polecenia tworzenia kopii zapasowej, zobacz [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Niedostępny warunek klucza zarządzany przez klienta

Podczas konfigurowania szyfrowania danych za pomocą klucza zarządzanego przez klienta w uchoniem programu Key Vault wymagany jest ciągły dostęp do tego klucza, aby serwer pozostał w trybie online. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w ucho. Serwer wydaje odpowiedni komunikat o błędzie i zmienia stan serwera *na Niedostępny.* Jedyną czynnością dozwoloną w bazie danych w tym stanie jest usunięcie go.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Przypadkowe odwołanie dostępu do kluczy z usługi Key Vault

Może się zdarzyć, że osoba posiadająca wystarczające prawa dostępu do usługi Key Vault przypadkowo wyłączy dostęp serwera do klucza przez:

* Odwoływanie uprawnień get, wrapKey i unwrapKey z serwera.
* Usunięcie klucza.
* Usuwanie magazynu kluczy.
* Zmiana reguł zapory magazynu kluczy.

* Usuwanie tożsamości zarządzanej serwera w usłudze Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorowanie klucza zarządzanego przez klienta w magazynie kluczy

Aby monitorować stan bazy danych i włączyć alerty o utracie dostępu do ochrony szyfrowania przezroczystych danych, skonfiguruj następujące funkcje platformy Azure:

* [Kondycja zasobów platformy Azure:](../service-health/resource-health-overview.md)niedostępna baza danych, która utraciła dostęp do klucza klienta, jest wyświetlana jako "Niedostępna" po odrzuceniu pierwszego połączenia z bazą danych.
* [Dziennik aktywności:](../service-health/alerts-activity-log-service-notifications.md)Gdy dostęp do klucza klienta w magazynie kluczy zarządzanym przez klienta nie powiedzie się, wpisy są dodawane do dziennika działań. Dostęp można przywrócić tak szybko, jak to możliwe, jeśli utworzysz alerty dla tych zdarzeń.

* [Grupy akcji:](../azure-monitor/platform/action-groups.md)Zdefiniuj je, aby wysyłać powiadomienia i alerty na podstawie twoich preferencji.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Przywracanie i replikowanie za pomocą klucza zarządzanego klienta w magazynie kluczy

Po zaszyfrowywanie usługi Azure Database for MySQL przy pomocą klucza zarządzanego klienta przechowywanego w usłudze Key Vault każda nowo utworzona kopia serwera jest również szyfrowana. Tę nową kopię można wykonać za pomocą operacji lokalnego lub przywracania geograficznego lub replik odczytu. Jednak kopię można zmienić, aby odzwierciedlić klucz zarządzany nowego klienta do szyfrowania. Po zmianie klucza zarządzanego przez klienta stare kopie zapasowe serwera rozpoczynają się przy użyciu najnowszego klucza.

Aby uniknąć problemów podczas konfigurowania szyfrowania danych zarządzanych przez klienta podczas tworzenia repliki przywracania lub odczytu, należy wykonać następujące kroki na serwerach wzorcowych i przywróconych/replik:

* Inicjowanie procesu tworzenia repliki przywracania lub odczytu z głównej bazy danych azure dla mysql.
* Nowo utworzony serwer (przywrócony/replika) pozostaje w stanie niedostępnym, ponieważ jego unikatowa tożsamość nie została jeszcze przyznana uprawnieniom do usługi Key Vault.
* Na serwerze przywrócony/replika ponownie zaatruj certyfikat klucza zarządzanego przez klienta w ustawieniach szyfrowania danych. Gwarantuje to, że nowo utworzony serwer otrzymuje uprawnienia do zawijania i odwijania klucza do klucza przechowywanego w magazynie kluczy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla bazy danych platformy Azure dla mysql przy użyciu portalu Azure.](howto-data-encryption-portal.md)

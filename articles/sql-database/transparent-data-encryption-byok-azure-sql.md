---
title: Funkcja TDE - Bring Your Own Key (BYOK) — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Bring Your Own Key (BYOK) pomocy technicznej dla przezroczystego szyfrowania danych (TDE) za pomocą usługi Azure Key Vault dla bazy danych SQL Database i Data Warehouse. Szyfrowanie TDE w bazie BYOK Przegląd, korzyści, jak to działa, zagadnienia i zalecenia.
keywords: ''
services: sql-database
documentationcenter: ''
author: aliceku
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: d3e8d34599600512a1d9d0308c0d6014691bf519
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442726"
---
# <a name="transparent-data-encryption-with-bring-your-own-key-support-for-azure-sql-database-and-data-warehouse"></a>Transparent Data Encryption z obsługą Bring Your Own Key dla usługi Azure SQL Database i Data Warehouse

Bring Your Own Key (BYOK), obsługa [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption) umożliwia zaszyfrowanie klucza szyfrowania bazy danych (DEK) przy użyciu klucza asymetrycznego, nazywane ochrony TDE.  Funkcja ochrony TDE znajduje się pod kontrolą w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), platformy Azure opartej na chmurze zewnętrznego kluczy systemu zarządzania. Usługa Azure Key Vault to pierwsze Usługa zarządzania kluczami, za pomocą którego TDE została zintegrowana obsługa dla funkcji BYOK. Funkcja TDE klucza szyfrowania danych, który jest przechowywany na stronę rozruchu bazy danych zostaje zaszyfrowany i odszyfrować za pomocą funkcji ochrony TDE. Funkcja ochrony TDE jest przechowywany w usłudze Azure Key Vault i nigdy nie opuszcza magazynu kluczy. Odwołania dostępu do magazynu kluczy z serwera bazy danych nie można odszyfrować i odczytu do pamięci.  Funkcja ochrony TDE jest ustawiona na poziomie serwera logicznego i jest dziedziczona przez wszystkie bazy danych skojarzonych z tym serwerem. 

Z obsługą funkcji BYOK użytkownicy mogą teraz kontrolować zadania zarządzania kluczami, w tym wymiany kluczy, uprawnień usługi key vault, usuwanie kluczy i Włącz inspekcję/raportowanie na wszystkie funkcje ochrony TDE, za pomocą funkcji usługi Azure Key Vault. Usługa Key Vault umożliwia centralne zarządzanie kluczami, wykorzystuje ściśle monitorowanych sprzętowych modułach zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między zarządzanie kluczami i danych może zapewnić zgodność z przepisami.  


Funkcja TDE z funkcją BYOK zapewnia następujące korzyści:
- Zwiększyć przejrzystość i kontrolę dzięki możliwości samodzielnie zarządzać ochrony TDE   
- Centralne zarządzanie ochrony TDE (wraz z innymi kluczy i wpisów tajnych używanych w innych usługach platformy Azure), udostępniając je w usłudze Key Vault
- Podział odpowiedzialności klucza i danych zarządzania w organizacji, aby obsługiwać podział obowiązków
- Większe zaufanie z własnych klientów, ponieważ usługa Key Vault jest zaprojektowana tak, aby firma Microsoft nie jest w stanie zobaczyć ani wyodrębnić jakiekolwiek klucze szyfrowania. 
- Obsługa wymiany kluczy

> [!IMPORTANT]
> Dla osób, za pomocą TDE zarządzane przez usługę, który chcesz rozpocząć korzystanie z usługi Key Vault TDE pozostaje włączony nawet w trakcie procesu przełączanie za pośrednictwem do ochrony TDE w usłudze Key Vault. Nie ma żadnych przestojów ani ponownie szyfrować pliki bazy danych. Przełączanie z klucza zarządzanego przez usługę do klucz usługi Key Vault wymaga jedynie ponownego szyfrowania klucza szyfrowania bazy danych (klucz szyfrowania danych), który jest operacją szybko i w trybie online. 
>

## <a name="how-does-tde-with-byok-support-work"></a>Jak funkcja TDE z funkcją BYOK obsługuje pracę?
 
![Uwierzytelnianie na serwerze do magazynu kluczy](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Gdy TDE najpierw jest skonfigurowany do używania funkcji ochrony TDE z usługi Key Vault, serwer wysyła klucza szyfrowania danych w poszczególnych baz danych z włączoną funkcję TDE do usługi Key Vault dla zawijania żądania klucza. Usługa Key Vault zwraca klucz szyfrowania szyfrowanej bazy danych, który jest przechowywany w bazie danych użytkownika.  

>[!IMPORTANT]
>Ważne jest, aby pamiętać, że **po ochrony TDE jest przechowywany w usłudze Azure Key Vault, nigdy nie opuszcza usługi Azure Key Vault**. Serwer logiczny można wysyłać żądania operacja klucza tylko do materiału klucza funkcji ochrony TDE w ramach usługi Key Vault i **nigdy nie uzyskuje dostępu do lub buforuje ochrony TDE**. Administrator usługi Key Vault ma prawo do odwołania uprawnień usługi Key Vault serwera w dowolnym momencie, w którym to przypadku wszystkie połączenia z serwerem są obcinane. 
>


## <a name="guidelines-for-configuring-tde-with-byok"></a>Wskazówki dotyczące konfigurowania funkcji TDE z funkcją BYOK

### <a name="general-guidelines"></a>Ogólne wskazówki
- Upewnij się, będzie znajdować się w tej samej dzierżawie usługi Azure Key Vault i Azure SQL Database.  Klucza magazynu i serwera interakcje międzydzierżawowa **nie są obsługiwane**.
- Zdecyduj, subskrypcje, które mają służyć do wymaganych zasobów — Przenoszenie serwera między subskrypcjami później wymaga nowe ustawienia funkcji TDE z BYOKs. Dowiedz się więcej o [przenoszenia zasobów](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-move-resources)
- Podczas konfigurowania funkcji TDE z funkcją BYOK, należy wziąć pod uwagę obciążenia umieszczone w magazynie kluczy przez powtarzanych opakowywanie/odpakowywania operacje. Na przykład ponieważ wszystkie bazy danych skojarzone z serwerem logicznym używa tych samych funkcji ochrony TDE, pracy w trybie failover tego serwera spowoduje wyzwolenie wiele kluczowych operacji dotyczących magazynu, ponieważ istnieją to bazy danych na serwerze. Oparte na naszych doświadczeniach i udokumentowane [klucza magazynu limity](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-service-limits), firma Microsoft zaleca kojarzenie co najwyżej 500 Standard / ogólnego przeznaczenia lub 200 Premium / krytyczne dla działania firmy baz danych przy użyciu jednej usługi Azure Key Vault w ramach jednej subskrypcji do zapewnienia dostępność charakteryzujące się stałym wysokim podczas uzyskiwania dostępu do funkcji ochrony TDE w magazynie. 
- Zalecane: Przechowywać kopię ochrony TDE w środowisku lokalnym.  Wymaga urządzenia HSM, aby utworzyć lokalnie funkcja ochrony TDE i systemem depozytu klucza do przechowywania kopii lokalnej ochrony TDE.  Dowiedz się, [jak przenieść klucz z lokalnego modułu HSM do usługi Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Wskazówki dotyczące konfigurowania usługi Azure Key Vault

- Tworzenie magazynu kluczy przy użyciu [opcji soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) włączone do ochrony przed utratą w razie przypadkowego klucz — lub usługi key vault — usunięcie.  Należy użyć [programu PowerShell, aby włączyć właściwość "opcji soft-delete"](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell) w magazynie kluczy (Ta opcja nie jest dostępna z portalu AKV jeszcze — ale wymagane przez program SQL):  
  - Nietrwale usunięte zasoby są przechowywane przez pewien okres czasu, 90 dni, chyba że są one odzyskać lub usunięte.
  - **Odzyskać** i **przeczyścić** akcji ma własne uprawnienia skojarzone zasady dostępu magazynu kluczy. 
- Ustaw blokady zasobu magazynu kluczy w celu kontrolowania, kto może usunąć ten zasób krytycznych i aby zapobiec przypadkowym lub nieautoryzowanych usunięciem.  [Dowiedz się więcej na temat blokowania zasobów](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources)

- Udziel dostępu serwer logiczny, do usługi key vault przy użyciu tożsamości usługi Azure Active Directory (Azure AD).  Korzystając z interfejsu użytkownika portalu, automatycznie tworzony tożsamości usługi Azure AD i uprawnień dostępu do magazynu kluczy są przyznawane na serwerze.  Przy użyciu programu PowerShell, aby skonfigurować funkcję TDE z funkcją BYOK, tożsamością usługi Azure AD muszą być tworzone i należy zweryfikować ukończenia. Zobacz [skonfigurować szyfrowanie TDE w bazie BYOK](transparent-data-encryption-byok-azure-sql-configure.md) szczegółowe instrukcje krok po kroku, korzystając z programu PowerShell.

  >[!NOTE]
  >Jeśli usługa Azure AD Identity **jest przypadkowo usunięty lub uprawnienia do serwera zostaną odwołane** za pomocą zasad dostępu magazynu kluczy, serwer utraci dostęp do magazynu kluczy i baz danych funkcja TDE szyfrowane są porzucane w ciągu 24 godzin.
  >

- Konfigurowanie usługi Azure Key Vault bez sieci Wirtualnej lub zapory.  Jeśli SQL utraci dostęp do magazynu kluczy, baz danych funkcja TDE szyfrowane są porzucane w ciągu 24 godzin.
- Włączanie inspekcji i raporty dotyczące wszystkich kluczy szyfrowania: Key Vault zapewnia dzienników, które są łatwe do dodania do innych informacjami i zdarzeniami (SIEM) narzędzia do zarządzania zabezpieczeniami. Operations Management Suite (OMS) [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) jest przykładem usługi, która jest już zintegrowana.
- W celu zapewnienia wysokiej dostępności szyfrowanymi bazami danych, należy skonfigurować każdy serwer logiczny za pomocą dwóch usługi Azure Key Vault, które znajdują się w różnych regionach.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key-stored-in-azure-key-vault"></a>Wskazówki dotyczące konfigurowania ochrony TDE (klucz asymetryczny) przechowywanych w usłudze Azure Key Vault

- Utwórz klucz szyfrowania lokalnie na lokalnym urządzeniu sprzętowego modułu zabezpieczeń. Upewnij się, że jest to klucz asymetryczny, szyfrowania RSA 2048, więc możliwą w usłudze Azure Key Vault.
- Zdeponowania klucza w systemie depozytu klucza.  
- Importowanie pliku klucza szyfrowania (pfx, .byok lub .backup) do usługi Azure Key Vault. 
    

>[!NOTE] 
    >Do celów testowych, istnieje możliwość utworzyć klucz w usłudze Azure Key Vault, jednak nie może być zdeponowane tego klucza, ponieważ klucz prywatny można nigdy nie opuszczają magazynu kluczy.  Zawsze utworzyć kopię zapasową i zdeponowania klucze używane do szyfrowania danych produkcyjnych jako utraty klucza (przypadkowego usunięcia klucza magazynu, wygaśnięcia itp.) powoduje utratę danych trwałych.
    >
    
- Użyj klucza bez daty wygaśnięcia — i nigdy nie Ustaw datę wygaśnięcia na kluczu już w użyciu: **po wygaśnięciu klucza szyfrowanymi bazami danych utracą dostęp do swoich funkcji ochrony TDE i są porzucane w ciągu 24 godzin**.
- Upewnij się, klucz jest włączone i ma uprawnienia do wykonywania *uzyskać*, *opakuj klucz*, i *odpakuj klucz* operacji.
- Przed użyciem klucza w usłudze Azure Key Vault po raz pierwszy, należy utworzyć kopię zapasową klucza usługi Azure Key Vault. Dowiedz się więcej o [Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) polecenia.
- Utwórz nową kopię zapasową, zawsze wtedy, gdy wszystkie zmiany zostały wprowadzone do klucza (na przykład dodać listy ACL, dodawanie tagów, Dodaj kluczowych atrybutów).
- **Zachowaj poprzednie wersje** klucza w magazynie kluczy, w przypadku rotacji kluczy, więc starsze kopie zapasowe bazy danych mogą zostać przywrócone. Po zmianie ochrony TDE dla bazy danych, a stare kopie zapasowe bazy danych **nie są aktualizowane** używać najnowszych funkcji ochrony TDE.  Każda kopia zapasowa wymaga ochrony TDE, został utworzony w czasie przywracania. Wymiany kluczy mogą być wykonywane zgodnie z instrukcjami w artykule [Obróć przezroczyste funkcja ochrony szyfrowania danych przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Zachowaj wszystkie klucze wcześniej używanych w usłudze Azure Key Vault, po zmianie kluczy zarządzanych przez usługę.  Dzięki temu kopie zapasowe bazy danych można przywrócić przy użyciu funkcji ochrony TDE, przechowywane w usłudze Azure Key Vault.  Funkcji ochrony TDE utworzonych za pomocą usługi Azure Key Vault musi zachować, dopóki wszystkie przechowywane kopie zapasowe utworzono przy użyciu kluczy zarządzanych przez usługę.  
- Tworzenia kopii zapasowych do odzyskania tych kluczy przy użyciu [Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Aby usunąć mogą mieć złamane zabezpieczenia klucza podczas zdarzenia zabezpieczeń, bez ryzyka utraty danych, wykonaj kroki opisane w temacie [Usuń klucz mogą mieć złamane zabezpieczenia](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).


## <a name="high-availability-geo-replication-and-backup--restore"></a>Wysoką dostępność, replikacja geograficzna i Backup / Restore

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Sposób konfigurowania wysokiej dostępności przy użyciu usługi Azure Key Vault jest zależna od konfiguracji bazy danych i serwera logicznego, a w tym miejscu są zalecanym konfiguracjom dotyczącym dwa różne przypadki.  Pierwszy przypadek jest autonomicznej bazy danych lub serwer logiczny za pomocą nie nadmiarowość geograficzna skonfigurowany.  Drugim przypadku jest bazy danych lub serwer logiczny skonfigurowany za pomocą grupy trybu failover i nadmiarowości geograficznej, gdzie należy upewnić się, że każdy geograficznie nadmiarowych kopii ma lokalne usługi Azure Key Vault w grupie trybu failover, co zapewnia pracy awaryjnej geo. W pierwszym przypadku jeśli potrzebujesz wysokiej dostępności bazy danych i serwer logiczny za pomocą nie skonfigurowanego nadmiarowość geograficzna, wysoce zalecane jest aby skonfigurować serwer do korzystania z dwóch różnych magazynów kluczy w dwóch różnych regionach za pomocą tego samego materiału klucza.  Można to osiągnąć, tworząc TDE ochrony za pomocą podstawowej usługi Key Vault wspólnie w tym samym regionie co serwer logiczny i klonowanie klucz do magazynu kluczy w innym regionie platformy Azure, więc, że serwer ma dostęp do drugiego magazynu kluczy powinien klucz podstawowy v myślnej środowisko ulegnie awarii, gdy baza danych jest uruchomiona. Użyj polecenia cmdlet Backup-AzureKeyVaultKey można pobrać klucza w zaszyfrowanym formacie z podstawowego magazynu kluczy, a następnie użyj polecenia cmdlet Restore-AzureKeyVaultKey i określić magazyn kluczy w drugim regionie.


![Pojedynczego serwera zaświadczanie o kondycji i geo-dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Jak skonfigurować Geo-DR za pomocą usługi Azure Key Vault

Aby zapewnić wysoką dostępność funkcji ochrony TDE szyfrowanymi bazami danych, jest wymagany do skonfigurowania nadmiarowego usługi Azure Key Vault na podstawie istniejących lub żądane grupy trybu failover bazy danych SQL Database lub wystąpienia aktywnej replikacji geograficznej.  Każdy serwer z replikacją geograficzną wymaga oddzielnych magazynu kluczy, który musi być kolokowany z serwerem, w tym samym regionie platformy Azure. Należy podstawowej bazy danych stają się niedostępne z powodu awarii w jednym regionie i trybu failover zostaje wyzwolona, pomocniczej bazy danych jest w stanie do przejęcia, przy użyciu dodatkowej usługi key vault. 
 
W przypadku baz danych Geo-Replicated Azure SQL wymagane jest następująca konfiguracja usługi Azure Key Vault:
- Jednej podstawowej bazy danych z usługą key vault w regionie i jednej pomocniczej bazy danych w usłudze key vault w regionie. 
- Co najmniej jedną zapasową jest wymagany, obsługiwane są maksymalnie cztery pomocnicze bazy danych. 
- Repliki pomocnicze bazy danych (łańcuch) nie są obsługiwane.

Poniższa sekcja zaczną się za pośrednictwem kroków instalacji i konfiguracji, które bardziej szczegółowo. 

### <a name="azure-key-vault-configuration-steps"></a>Kroki konfiguracji usługi Azure Key Vault

- Zainstaluj [programu PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0) 
- Tworzenie dwóch usługi Azure Key Vault w dwóch różnych regionach za pomocą [programu PowerShell, aby włączyć właściwość "opcji soft-delete"](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell) w magazynach kluczy (Ta opcja nie jest dostępna z portalu AKV jeszcze — ale wymagane przez program SQL).
- Obie usługi Azure Key Vault musi znajdować się w dwóch regionach, dostępne w tej samej lokalizacji geograficznej platformy Azure, aby kopia zapasowa i przywracanie kluczy do pracy.  Jeśli potrzebujesz dwóch magazynów kluczy muszą znajdować się w różnych obszarów geograficznych, aby spełniać wymagania SQL Geo-DR, postępuj zgodnie z [procesu BYOK](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys) umożliwiająca klucze do zaimportowania z lokalnego modułu HSM.
- Utwórz nowy klucz w pierwszej usłudze key vault:  
  - Klucz RSA/RSA-HSA 2048 
  - Nie daty wygaśnięcia 
  - Klucz jest włączone i ma uprawnienia do wykonania get, opakuj klucz i odpakuj kluczowych operacji 
- Tworzenie kopii zapasowej klucz podstawowy i przywróć klucz do drugiego magazynu kluczy.  Zobacz [BackupAzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) i [Restore-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0). 

### <a name="azure-sql-database-configuration-steps"></a>Kroki konfiguracji bazy danych Azure SQL

Następujące kroki konfiguracji różnią się, czy zaczyna się za pomocą nowego wdrożenia programu SQL, lub jeśli działa z już istniejącym wdrożeniem SQL Geo-DR.  Firma Microsoft najpierw opisują kroki konfiguracji dla nowego wdrożenia i wyjaśniono, jak przypisać funkcji ochrony TDE, przechowywane w usłudze Azure Key Vault do już istniejącego wdrożenia, który posiada już link Geo-DR ustanowione. 

Kroki umożliwiające nowego wdrożenia:
- Utwórz dwa logiczne serwery SQL, w tym samym dwóch regionach jako utworzone wcześniej magazynów kluczy. 
- Wybierz w okienku TDE serwera logicznego i dla każdego serwera logicznego SQL:  
   - Wybierz AKV w tym samym regionie 
   - Wybierz klucz do użycia jako funkcja ochrony TDE — każdy serwer użyje kopii lokalnej ochrony TDE. 
   - W ten sposób w portalu zostanie utworzona [AppID](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/overview) dla serwera logicznego SQL, który jest używany do przypisywania logiczne uprawnienia programu SQL Server do dostępu do magazynu kluczy — nie usuwaj tej tożsamości. Zamiast usuwania uprawnień w usłudze Azure Key Vault dla serwera logicznego SQL, który jest używany do przypisywania logiczne uprawnienia programu SQL Server do dostępu do magazynu kluczy może odwołać dostęp.
- Tworzenie podstawowej bazy danych. 
- Postępuj zgodnie z [wskazówki aktywnej replikacji geograficznej](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) ukończyć wykonywanie scenariusza, ten krok spowoduje utworzenie pomocniczej bazy danych.

![Grupy trybu failover i geo-dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

>[!NOTE]
>Należy upewnić się, że ten sam funkcji ochrony TDE w obu magazynów kluczy, przed przejściem do ustanawiania połączenia geograficzna między bazami danych.
>

Kroki umożliwiające istniejącej bazy danych SQL przy użyciu Geo-DR wdrażania:

Ponieważ logiczne serwerów SQL już istnieje, a podstawowych i pomocniczych baz danych są już przypisane, kroki, aby skonfigurować usługę Azure Key Vault odbywa się w następującej kolejności: 
- Uruchom przy użyciu serwera logicznego SQL, który jest hostem pomocniczej bazy danych: 
   - Przypisz magazyn kluczy znajdujących się w tym samym regionie 
   - Przypisywanie ochrony TDE 
- Teraz przejdź do serwera logicznego SQL, który jest hostem podstawowej bazy danych: 
   - Wybierz tej samej funkcji ochrony TDE jako używane dla pomocniczej bazy danych
   
![Grupy trybu failover i geo-dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Podczas przypisywania magazynu kluczy do serwera, należy uruchomić z serwera pomocniczego.  W drugim kroku Przypisz klucza magazynu do serwera podstawowego i aktualizowanie ochrony TDE łącze Geo-DR będą nadal działać, ponieważ na tym etapie ochrony TDE, używane przez zreplikowanej bazy danych nie jest dostępny dla obu serwerów.
>

Przed Włączanie funkcji TDE z klientem zarządzane klucze chronione w usłudze Azure Key Vault dla scenariusza bazy danych SQL Geo-DR, jest ważne, aby tworzyć i obsługiwać dwie usługi Azure Key Vault z identyczną zawartość w tych samych regionach, które będą używane dla replikacji geograficznej bazy danych SQL.  "Identyczną zawartość" specjalnie oznacza, że zarówno magazynów kluczy musi zawierać kopie tych samych Protector(s) TDE tak, aby oba serwery mają dostęp do użycia funkcji ochrony TDE przez wszystkie bazy danych.  Idąc dalej, jest to wymagane, aby zachować zarówno magazynów kluczy w synchronizacji, co oznacza, że mogą zawierać kopie tych samych funkcji ochrony TDE po wymiany kluczy, obsługa starsze wersje programu kluczami używanymi na potrzeby plików dziennika lub funkcji ochrony TDE kopii zapasowych należy zachować te same właściwości kluczy oraz klucz Magazyny należy zachować te same uprawnienia dostępu do języka SQL.  
 
Postępuj zgodnie z instrukcjami w [omówienie aktywnej replikacji geograficznej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) do testowania i wyzwolić tryb failover, który ma się odbywać w regularnych odstępach czasu, aby potwierdzić uprawnienia dostępu do języka SQL do obu magazynów kluczy mają została zachowana. 


### <a name="backup-and-restore"></a>Wykonywanie kopii zapasowych i przywracanie

Po bazy danych jest szyfrowana za pomocą funkcji TDE przy użyciu klucza z usługi Key Vault, żadnych wygenerowanego kopii zapasowych również są szyfrowane przy użyciu tych samych funkcji ochrony TDE.

Aby przywrócić z kopii zapasowej zaszyfrowany przy użyciu funkcji ochrony TDE z usługi Key Vault, upewnij się, że materiału klucza jest nadal w oryginalnym magazynie pod oryginalną nazwę klucza. Po zmianie ochrony TDE dla bazy danych, a stare kopie zapasowe bazy danych **nie** zaktualizowane pod kątem korzystania z najnowszych funkcji ochrony TDE. Dlatego zaleca się zachować wszystkie starsze wersje ochrony TDE w usłudze Key Vault, dzięki czemu można przywrócić kopii zapasowych bazy danych. 

Jeśli klucz, który może okazać się niezbędne do przywrócenia kopii zapasowej nie jest już w jego oryginalnym magazynie kluczy, zwracany jest następujący komunikat o błędzie: "serwer docelowy <Servername> nie ma dostępu do wszystkich identyfikatorów URI AKV utworzone między < sygnatura czasowa #1 > i < sygnatura czasowa #2 >. Ponów próbę operacji po przywróceniu wszystkie identyfikatory URI AKV".

Aby rozwiązać ten problem, uruchom [Get AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) polecenia cmdlet, aby zwrócić listę kluczy z usługi Key Vault, które zostały dodane do serwera (chyba że zostały usunięte przez użytkownika). Aby upewnić się, że wszystkie kopie zapasowe, które mogą zostać przywrócone, upewnij się, że serwer docelowy dla kopii zapasowej ma dostęp do wszystkich tych kluczy.

   ```powershell
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej dla bazy danych SQL, zobacz [odzyskać bazę danych Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups). Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej dla usługi SQL Data Warehouse, zobacz [odzyskiwania usługi Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview).


Dodatkowego rozważenia kopię zapasową plików dziennika: kopii zapasowej dziennika pliki pozostają zaszyfrowane przy użyciu oryginalnego modułu szyfrującego TDE, nawet jeśli została obracać ochrony TDE i baza danych jest teraz przy użyciu nowej ochrony TDE.  W czasie przywracania oba klucze będą potrzebne do przywrócenia bazy danych.  Jeśli plik dziennika jest za pomocą ochrony TDE, przechowywane w usłudze Azure Key Vault, ten klucz będzie potrzebna w czasie przywracania, nawet, jeśli baza danych została zmieniona w tym samym czasie używać TDE zarządzane przez usługę.



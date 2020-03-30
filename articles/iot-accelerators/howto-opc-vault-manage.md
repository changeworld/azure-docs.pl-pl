---
title: Jak zarządzać usługą certyfikatów OPC Vault — Azure | Dokumenty firmy Microsoft
description: Zarządzanie certyfikatami głównego urzędu certyfikacji usługi OPC Vault i uprawnieniami użytkowników.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203655"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Zarządzanie usługą certyfikatów OPC Vault

W tym artykule opisano zadania administracyjne usługi zarządzania certyfikatami usługi OPC Vault na platformie Azure. Zawiera informacje o tym, jak odnowić certyfikaty urzędu certyfikacji wystawcy, jak odnowić listę odwołania certyfikatów (CRL) oraz jak udzielić i odwołać dostęp użytkowników.

## <a name="create-or-renew-the-root-ca-certificate"></a>Tworzenie lub odnawianie głównego certyfikatu urzędu certyfikacji

Po wdrożeniu programu OPC Vault należy utworzyć główny certyfikat urzędu certyfikacji. Bez prawidłowego certyfikatu urzędu certyfikacji wystawcy nie można podpisywać ani wystawiać certyfikatów aplikacji. Zapoznaj się z [certyfikatami,](howto-opc-vault-secure-ca.md#certificates) aby zarządzać certyfikatami z rozsądnymi, bezpiecznymi okresami istnienia. Odnowienie certyfikatu urzędu certyfikacji wystawcy po połowie jego okresu istnienia. Podczas odnawiania należy również wziąć pod uwagę, że skonfigurowany okres istnienia nowo podpisanego certyfikatu aplikacji nie powinien przekraczać okresu istnienia certyfikatu urzędu certyfikacji wystawcy.
> [!IMPORTANT]
> Rola Administratora jest wymagana do utworzenia lub odnowienia certyfikatu urzędu certyfikacji wystawcy.

1. Otwórz usługę certyfikatów w `https://myResourceGroup-app.azurewebsites.net`punkcie , i zaloguj się.
2. Przejdź do **grup certyfikatów**.
3. Na liście znajduje się jedna domyślna grupa certyfikatów. Wybierz pozycję **Edit** (Edytuj).
4. W **temacie Edytowanie szczegółów grupy certyfikatów**można zmodyfikować nazwę podmiotu i okres istnienia urzędu certyfikacji oraz certyfikaty aplikacji. Temat i okresy istnienia należy ustawić tylko raz przed wystawieniem pierwszego certyfikatu urzędu certyfikacji. Zmiany okresu istnienia podczas operacji mogą spowodować niespójne okresy istnienia wystawionych certyfikatów i list CRL.
5. Wprowadź prawidłowy temat (na przykład `CN=My CA Root, O=MyCompany, OU=MyDepartment`).<br>
   > [!IMPORTANT]
   > Jeśli zmienisz temat, musisz odnowić certyfikat wystawcy lub usługa nie będzie podpisywać certyfikatów aplikacji. Temat konfiguracji jest porównywany z tematem aktywnego certyfikatu Emitenta. Jeśli tematy nie są zgodne, odmowa podpisania certyfikatu.
6. Wybierz **pozycję Zapisz**.
7. Jeśli w tym momencie wystąpi "zabroniony" błąd, poświadczenia użytkownika nie mają uprawnień administratora do modyfikowania lub tworzenia nowego certyfikatu głównego. Domyślnie użytkownik, który wdrożył usługę ma administratora i podpisywania ról z usługą. Inni użytkownicy muszą zostać dodana do ról osoby zatwierdzającej, modułu zapisującego lub administratora, stosownie do przypadku w rejestracji aplikacji usługi Azure Active Directory (Azure AD).
8. Wybierz **pozycję Szczegóły**. Powinno to pokazać zaktualizowane informacje.
9. Wybierz **pozycję Odnów certyfikat urzędu certyfikacji,** aby wystawić pierwszy certyfikat urzędu certyfikacji wystawcy lub odnowić certyfikat wystawcy. Następnie wybierz przycisk **OK**.
10. Po kilku sekundach zobaczysz **szczegóły certyfikatu**. Aby pobrać najnowszy certyfikat urzędu certyfikacji i platformę CRL do dystrybucji do aplikacji OPC UA, wybierz **pozycję Wystawca** lub **Crl**.

Teraz usługa zarządzania certyfikatami OPC UA jest gotowa do wystawiania certyfikatów dla aplikacji OPC UA.

## <a name="renew-the-crl"></a>Odnawianie umowy CRL

Odnowienie karty CRL jest aktualizacją, która powinna być rozdzielana do aplikacji w regularnych odstępach czasu. Urządzenia OPC UA, które obsługują rozszerzenie punktu dystrybucji listy CRL X509, można bezpośrednio zaktualizować crl z punktu końcowego mikrousługi. Inne urządzenia OPC UA mogą wymagać ręcznych aktualizacji lub mogą być aktualizowane za pomocą rozszerzeń wypychania serwera GDS (*), aby zaktualizować listy zaufania certyfikatami i listami CRL.

W następującym przepływie pracy wszystkie żądania certyfikatów w usuniętych stanach są odwoływane w listach CRL, które odpowiadają certyfikatowi urzędu certyfikacji wystawcy, dla którego zostały wystawione. Numer wersji listu CRL jest zwiększany o 1. <br>
> [!NOTE]
> Wszystkie wydane listy CRL są ważne do momentu wygaśnięcia certyfikatu urzędu certyfikacji wystawcy. Dzieje się tak, ponieważ specyfikacja OPC UA nie wymaga obowiązkowego, deterministycznego modelu dystrybucji dla listy CRL.

> [!IMPORTANT]
> Rola Administratora jest wymagana do odnowienia umowy CRL wystawy.

1. Otwórz usługę certyfikatów w `https://myResourceGroup.azurewebsites.net`punkcie , i zaloguj się.
2. Przejdź do strony **Grupy certyfikatów.**
3. Wybierz **pozycję Szczegóły**. Powinno to wyświetlać bieżący certyfikat i informacje o programie CRL.
4. Wybierz **pozycję Aktualizuj listę odwołania crl (CRL),** aby wystawić zaktualizowaną listę CRL dla wszystkich aktywnych certyfikatów wystawców w magazynie OPC Vault.
5. Po kilku sekundach zobaczysz **szczegóły certyfikatu**. Aby pobrać najnowszy certyfikat urzędu certyfikacji i platformę CRL do dystrybucji do aplikacji OPC UA, wybierz **pozycję Wystawca** lub **Crl**.

## <a name="manage-user-roles"></a>Zarządzanie rolami użytkowników

Zarządzanie rolami użytkowników dla mikrousługi OPC Vault w aplikacji Azure AD Enterprise. Aby uzyskać szczegółowy opis definicji ról, zobacz [Role](howto-opc-vault-secure-ca.md#roles).

Domyślnie uwierzytelniony użytkownik w dzierżawie może zalogować się do usługi jako czytnik. Role o wyższych uprawnieniach wymagają ręcznego zarządzania w witrynie Azure portal lub przy użyciu programu PowerShell.

### <a name="add-user"></a>Dodawanie użytkownika

1. Otwórz witrynę Azure Portal.
2. Przejdź do **aplikacji Azure Active Directory** > **Enterprise**.
3. Wybierz rejestrację mikrousługi OPC Vault `resourceGroupName-service`(domyślnie twojej).
4. Przejdź do **użytkownika i grup**.
5. Wybierz **pozycję Dodaj użytkownika**.
6. Wybierz lub zaproś użytkownika do przypisania do określonej roli.
7. Wybierz rolę dla użytkowników.
8. Wybierz opcję **Przypisz**.
9. Dla użytkowników w roli Administrator lub osoba zatwierdzająca kontynuuj dodawanie zasad dostępu usługi Azure Key Vault.

### <a name="remove-user"></a>Usuwanie użytkownika

1. Otwórz witrynę Azure Portal.
2. Przejdź do **aplikacji Azure Active Directory** > **Enterprise**.
3. Wybierz rejestrację mikrousługi OPC Vault `resourceGroupName-service`(domyślnie twojej).
4. Przejdź do **użytkownika i grup**.
5. Wybierz użytkownika z rolą do usunięcia, a następnie wybierz pozycję **Usuń**.
6. W przypadku usuniętych użytkowników w roli Administrator lub osoba zatwierdzająca należy również usunąć ich z zasad usługi Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Dodawanie zasad dostępu użytkowników do usługi Azure Key Vault

Dodatkowe zasady dostępu są wymagane dla osób zatwierdzających i administratorów.

Domyślnie tożsamość usługi ma ograniczone uprawnienia dostępu do usługi Key Vault, aby zapobiec wysuniętym z wysokości operacji lub zmianom bez personifikacji użytkownika. Podstawowe uprawnienia usługi to Pobierz i Lista, zarówno dla wpisów tajnych, jak i certyfikatów. W przypadku wpisów tajnych istnieje tylko jeden wyjątek: usługa może usunąć klucz prywatny z tajnego magazynu po zaakceptowaniu go przez użytkownika. Wszystkie inne operacje wymagają uprawnień personifikowanych przez użytkownika.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku roli osoby zatwierdzającej do usługi Key Vault należy dodać następujące uprawnienia

1. Otwórz witrynę Azure Portal.
2. Przejdź do programu `resourceGroupName`OPC Vault używanego podczas wdrażania.
3. Przejdź do magazynu `resourceGroupName-xxxxx`kluczy .
4. Przejdź do **zasad dostępu**.
5. Wybierz **pozycję Dodaj nowy**.
6. Pomiń szablon. Nie ma szablonu, który odpowiada wymaganiom.
7. Wybierz **pozycję Wybierz podmiot zabezpieczeń**i wybierz użytkownika, który ma zostać dodany, lub zaproś nowego użytkownika do dzierżawy.
8. Wybierz następujące **uprawnienia klucza**: **Pobierz**, **Lista**i **Podpisz**.
9. Wybierz następujące **uprawnienia tajne:** **Pobierz**, **Lista**, **Ustaw**i **Usuń**.
10. Wybierz następujące **uprawnienia certyfikatu**: **Pobierz** i **lista**.
11. Wybierz **przycisk OK**i wybierz pozycję **Zapisz**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku roli administratora do usługi Key Vault należy dodać następujące uprawnienia:

1. Otwórz witrynę Azure Portal.
2. Przejdź do programu `resourceGroupName`OPC Vault używanego podczas wdrażania.
3. Przejdź do magazynu `resourceGroupName-xxxxx`kluczy .
4. Przejdź do **zasad dostępu**.
5. Wybierz **pozycję Dodaj nowy**.
6. Pomiń szablon. Nie ma szablonu, który odpowiada wymaganiom.
7. Wybierz **pozycję Wybierz podmiot zabezpieczeń**i wybierz użytkownika, który ma zostać dodany, lub zaproś nowego użytkownika do dzierżawy.
8. Wybierz następujące **uprawnienia klucza**: **Pobierz**, **Lista**i **Podpisz**.
9. Wybierz następujące **uprawnienia tajne:** **Pobierz**, **Lista**, **Ustaw**i **Usuń**.
10. Wybierz następujące **uprawnienia certyfikatu:** **Pobierz**, **Lista**, **Aktualizacja**, **Utwórz**i **Zaimportuj**.
11. Wybierz **przycisk OK**i wybierz pozycję **Zapisz**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Usuwanie zasad dostępu użytkowników z usługi Azure Key Vault

1. Otwórz witrynę Azure Portal.
2. Przejdź do programu `resourceGroupName`OPC Vault używanego podczas wdrażania.
3. Przejdź do magazynu `resourceGroupName-xxxxx`kluczy .
4. Przejdź do **zasad dostępu**.
5. Znajdź użytkownika do usunięcia i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz instrukcje zarządzania certyfikatami OPC Vault i użytkownikami, możesz:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja urządzeń OPC](howto-opc-vault-secure.md)

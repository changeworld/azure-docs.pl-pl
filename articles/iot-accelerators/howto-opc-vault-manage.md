---
title: Jak zarządzać usługą zarządzania certyfikatami magazynu OPC — Azure | Microsoft Docs
description: Zarządzanie certyfikatami głównego urzędu certyfikacji magazynu OPC i uprawnieniami użytkownika.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973807"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>Jak zarządzać usługą certyfikatów OPC UA

W tym artykule wyjaśniono zadania administracyjne dotyczące usługi zarządzania certyfikatami OPC UA na platformie Azure, jak odnowić certyfikaty urzędu certyfikacji wystawcy, jak odnowić listę odwołania certyfikatów (CRL) i sposób udzielania dostępu do użytkowników oraz odwoływania się do nich.

## <a name="create-or-renew-the-root-ca-certificate"></a>Utwórz lub odnów certyfikat głównego urzędu certyfikacji

Do utworzenia certyfikatu głównego urzędu certyfikacji jest wymagany krok po wdrożeniu. Bez prawidłowego certyfikatu urzędu certyfikacji wystawcy nie można podpisać ani wystawić certyfikatów aplikacji.<br>Zapoznaj się z rozdziałem dotyczącym [okresów istnienia certyfikatów](howto-opc-vault-secure-ca.md#certificates) , aby zarządzać certyfikatami przy użyciu rozsądnych, bezpiecznych okresów istnienia. Certyfikat urzędu certyfikacji wystawcy powinien zostać odnowiony po połowie okresu istnienia, ale nie później niż przed upływem skonfigurowanego okresu istnienia nowo podpisanego certyfikatu aplikacji spowodowałoby przekroczenie okresu istnienia certyfikatu wystawcy.<br>
> [!IMPORTANT]
> Rola "Administrator" jest wymagana do utworzenia lub odnowienia certyfikatu urzędu certyfikacji wystawcy.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup-app.azurewebsites.net` i zaloguj się.
2. Przejdź do `Certificate Groups` strony.
3. Na liście znajduje `Default` się jedna grupa certyfikatów. Kliknij pozycję `Edit`.
4. W `Edit Certificate Group Details` programie można modyfikować nazwę podmiotu i okres istnienia urzędu certyfikacji oraz certyfikaty aplikacji.<br>Temat i okresy istnienia powinny być ustawiane tylko raz przed wystawieniem pierwszego certyfikatu urzędu certyfikacji. Zmiany okresu istnienia w trakcie operacji mogą spowodować niespójne okresy istnienia wystawionych certyfikatów i list CRL.
5. Wprowadź prawidłowy temat, na przykład `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Zmiana podmiotu wymaga odnowienia certyfikatu wydawcy lub usługa nie będzie mogła podpisać certyfikatów aplikacji. Podmiot konfiguracji jest Sanity sprawdzany pod względem tematu aktywnego certyfikatu wystawcy. Jeśli tematy nie są zgodne, odmówiono podpisania certyfikatu.
6. `Save` Kliknij przycisk.
7. Jeśli w tym momencie trafisz błąd "zabronione", poświadczenia użytkownika nie mają uprawnienia administratora do modyfikowania lub tworzenia nowego certyfikatu głównego. Domyślnie użytkownik, który wdrożył usługę, ma role administratora i podpisywania usługi, inni użytkownicy muszą zostać dodani do ról "osoba zatwierdzająca", "autor" lub "Administrator" zgodnie z potrzebami w ramach rejestracji aplikacji AzureAD.
8. `Details` Kliknij przycisk. `View Certificate Group Details` Powinny być wyświetlane zaktualizowane informacje.
9. `Renew CA Certificate` Kliknij przycisk, aby wystawić certyfikat pierwszego wystawcy urzędu certyfikacji lub odnowić certyfikat wystawcy. Naciśnij `Ok` klawisz, aby wykonać operację.
10. Po kilku sekundach `Certificate Details` są wyświetlane. Naciśnij `Issuer` lub`Crl` , aby pobrać najnowszy certyfikat urzędu certyfikacji i listę CRL na potrzeby dystrybucji do aplikacji OPC UA.
11. Teraz usługa zarządzania certyfikatami OPC UA jest gotowa do wystawiania certyfikatów dla aplikacji OPC UA.

## <a name="renew-the-crl"></a>Odnów listę CRL

Odnowienie listy odwołania certyfikatów (CRL) jest aktualizacją, która powinna być dystrybuowana do aplikacji w regularnych odstępach czasu. Urządzenia OPC UA, które obsługują rozszerzenie x509 punktu dystrybucji listy CRL, mogą bezpośrednio aktualizować listę CRL z punktu końcowego mikrousługi. Inne urządzenia OPC UA mogą wymagać ręcznej aktualizacji lub w najlepszym przypadku, można je zaktualizować za pomocą rozszerzeń wypychania serwera GDS (*), aby zaktualizować listy zaufania za pomocą certyfikatów i list CRL.

W następującym przepływie pracy wszystkie żądania certyfikatów w usuniętych Stanach są odwoływane na listach CRL, które odnoszą się do certyfikatu wystawionego przez urząd certyfikacji. Numer wersji listy CRL jest zwiększany o 1. <br>
> [!NOTE]
> Wszystkie wystawione listy CRL są ważne do momentu wygaśnięcia certyfikatu urzędu certyfikacji wystawcy, ponieważ Specyfikacja OPC UA nie wymaga obowiązkowego, deterministycznego modelu dystrybucji dla listy CRL.

> [!IMPORTANT]
> Rola "Administrator" jest wymagana do odnowienia listy CRL wystawcy.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup.azurewebsites.net` i zaloguj się.
2. Przejdź do `Certificate Groups` strony.
3. `Details` Kliknij przycisk. `View Certificate Group Details` Powinien zostać wyświetlony bieżący certyfikat i informacje o liście CRL.
4. Kliknij przycisk, `Update CRL Revocation List(CRL)` aby wystawić zaktualizowaną listę CRL dla wszystkich aktywnych certyfikatów wystawcy w magazynie magazynu OPC.
5. Po kilku sekundach `Certificate Details` są wyświetlane. Naciśnij `Issuer` lub`Crl` , aby pobrać najnowszy certyfikat urzędu certyfikacji i listę CRL na potrzeby dystrybucji do aplikacji OPC UA.

## <a name="manage-user-roles"></a>Zarządzanie rolami użytkowników

Role użytkowników dla mikrousługi magazynu OPC są zarządzane w aplikacji Azure Active Directory Enterprise.

Szczegółowy opis definicji ról można znaleźć w sekcji [role](howto-opc-vault-secure-ca.md#roles) .

Domyślnie uwierzytelniony użytkownik w dzierżawie może zalogować się w usłudze jako czytelnik. Wyższe role uprzywilejowane wymagają ręcznego zarządzania w Azure Portal lub przy użyciu programu PowerShell.

### <a name="add-user"></a>Dodawanie użytkownika

1. Otwórz Azure Portal pod adresem `portal.azure.com`.
2. Przejdź do strony `Azure Active Directory`/`Enterprise applications`.
3. Domyślnie wybierz pozycję Rejestracja mikrousługi magazynu OPC `resourceGroupName-service`.
4. Przejdź do adresu `Users and Groups`.
5. Kliknij pozycję `Add User`.
6. Wybierz lub Zaproś użytkownika o przypisanie do określonej roli.
7. Wybierz rolę dla użytkowników.
8. `Assign` Naciśnij przycisk.
9. W przypadku użytkowników `Administrator` w `Approver` ramach usługi lub roli Kontynuuj dodawanie Azure Key Vault zasad dostępu.

### <a name="remove-user"></a>Usuń użytkownika

1. Otwórz Azure Portal pod adresem `portal.azure.com`.
2. Przejdź do strony `Azure Active Directory`/`Enterprise applications`.
3. Domyślnie wybierz pozycję Rejestracja mikrousługi magazynu OPC `resourceGroupName-service`.
4. Przejdź do adresu `Users and Groups`.
5. Wybierz użytkownika z rolą do usunięcia.
6. `Remove` Naciśnij przycisk.
7. Usuń usunięte Administratorzy i osoby zatwierdzające również z zasad Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Dodawanie zasad dostępu użytkowników do Azure Key Vault

Dodatkowe zasady dostępu są wymagane dla **osób zatwierdzających** i **administratorów**.

Domyślnie tożsamość usługi ma tylko ograniczone uprawnienia dostępu Key Vault, aby zapobiec podniesionym operacjom lub zmianom, które mają zostać wykonane bez personifikacji użytkownika. Podstawowe uprawnienia usługi to `Get` i `List` zarówno dla kluczy tajnych, jak i certyfikatów. W przypadku wpisów tajnych istnieje tylko jeden wyjątek. usługa może `Delete` być kluczem prywatnym z magazynu wpisów tajnych po zaakceptowaniu przez użytkownika. Wszystkie inne operacje wymagają uprawnień personifikowanych przez użytkownika.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku **roli osoby zatwierdzającej** do Key Vault należy dodać następujące uprawnienia:

1. Otwórz Azure Portal pod adresem `portal.azure.com`.
2. Przejdź do magazynu `resourceGroupName`OPC używanego podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx`.
4. Przejdź do `Access Policies`.
5. Kliknij pozycję `Add new`.
6. Pomiń szablon, nie istnieje szablon, który spełnia wymagania.
7. Kliknij pozycję `Select Principal` włączone i wybierz użytkownika do dodania lub zaproszenia nowego użytkownika do dzierżawy.
8. Sprawdź `Key permissions` :`Get`i najważniejsze`Sign`. `List`
9. Sprawdź `Secret permissions`: `Get`, `List`, i.`Set` `Delete`
10. Sprawdź `Certificate permissions`: `Get`i .`List`
11. Kliknij pozycję `Ok` (Dalej).
12. `Save`wprowadzane.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku **roli administratora** następujące uprawnienia należy dodać do Key Vault:

1. Otwórz Azure Portal pod adresem `portal.azure.com`.
2. Przejdź do magazynu `resourceGroupName`OPC używanego podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx`.
4. Przejdź do `Access Policies`.
5. Kliknij pozycję `Add new`.
6. Pomiń szablon, nie istnieje szablon, który spełnia wymagania.
7. Kliknij pozycję `Select Principal` włączone i wybierz użytkownika do dodania lub zaproszenia nowego użytkownika do dzierżawy.
8. Sprawdź `Key permissions` :`Get`i najważniejsze`Sign`. `List`
9. Sprawdź `Secret permissions`: `Get`, `List`, i.`Set` `Delete`
10. Sprawdź `Certificate permissions`: `Get`, `List`, `Update`i. `Create` `Import`
11. Kliknij pozycję `Ok` (Dalej).
12. `Save`wprowadzane.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Usuń zasady dostępu użytkowników z Azure Key Vault

1. Otwórz Azure Portal pod adresem `portal.azure.com`.
2. Przejdź do magazynu `resourceGroupName`OPC używanego podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx`.
4. Przejdź do `Access Policies`.
5. Znajdź użytkownika, którego chcesz usunąć, i `... / Delete` kliknij przycisk Włącz, aby usunąć dostęp użytkownika.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak zarządzać certyfikatami i użytkownikami magazynu OPC, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja urządzeń OPC](howto-opc-vault-secure.md)

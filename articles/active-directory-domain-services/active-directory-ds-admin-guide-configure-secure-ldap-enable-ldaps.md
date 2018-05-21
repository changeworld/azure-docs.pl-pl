---
title: Skonfigurować bezpiecznego protokołu LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: maheshu
ms.openlocfilehash: 8da03990ace37b527553b0fe3ff0032515e1b812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD

## <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, przeprowadzisz [zadanie 2 - wyeksportowany certyfikat bezpiecznego LDAP. Plik PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Zadanie 3 — Włączanie bezpiecznego protokołu LDAP do domeny zarządzanej przy użyciu portalu Azure
Aby włączyć bezpiecznego protokołu LDAP, wykonaj następujące kroki konfiguracji:

1. Przejdź do  **[portalu Azure](https://portal.azure.com)**.

2. Wyszukaj "usługi domeny" w **wyszukiwania zasobów** pola wyszukiwania. Wybierz **usług domenowych Azure AD** z wyników wyszukiwania. **Usług domenowych Azure AD** strona zawiera listę domeny zarządzanej.

    ![Znajdź zainicjowanie obsługi domeny zarządzanej](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kliknij nazwę domeny zarządzanej (na przykład "contoso100.com"), aby zobaczyć więcej szczegółów dotyczących domeny.

    ![Usług domenowych — stan inicjowania obsługi](./media/getting-started/domain-services-provisioning-state.png)

3. Kliknij przycisk **Secure LDAP** w okienku nawigacji.

    ![Usługi domenowe — strona bezpiecznego protokołu LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Bezpieczny dostęp LDAP do domeny zarządzanej jest domyślnie wyłączona. Przełącz **bezpieczny protokół LDAP** do **włączyć**.

    ![Włącz bezpieczny protokół LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Domyślnie bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu jest wyłączona. Przełącz **zapewnia bezpieczny dostęp LDAP w Internecie** do **włączyć**, jeśli to konieczne. 

    > [!WARNING]
    > Po włączeniu bezpiecznego dostępu LDAP w Internecie domeny jest podatny na hasła ataków siłowych za pośrednictwem Internetu. Dlatego zaleca się skonfigurowanie grupy NSG do blokowania dostępu do zakresów adresów IP wymagana źródła. Zapoznaj się z instrukcjami, aby [blokowania dostępu LDAPS do domeny zarządzanej za pośrednictwem Internetu](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Kliknij ikonę następujący folder **. Plik PFX o bezpiecznego certyfikatu LDAP**. Określ ścieżkę do pliku PFX z certyfikatu na potrzeby bezpiecznego dostępu LDAP do domeny zarządzanej.

7. Określ **hasła do odszyfrowywania. Plik PFX**. Należy podać to samo hasło użyte podczas eksportowania certyfikatu do pliku PFX.

8. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać** przycisku.

9. Zobaczysz, że powiadomienie, które informuje, że bezpieczny protokół LDAP jest konfigurowany do domeny zarządzanej. Do czasu ukończenia tej operacji nie można modyfikować inne ustawienia domeny.

    ![Konfigurowanie bezpiecznego protokołu LDAP do domeny zarządzanej](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Włączanie bezpiecznego protokołu LDAP do domeny zarządzanej trwa około 10 – 15 minut. Certyfikatu podanego LDAP bezpiecznego niezgodna kryteriów wymaganych dla katalogu nie włączono bezpiecznego protokołu LDAP i zostanie wyświetlony błąd. Na przykład nazwa domeny jest nieprawidłowa, certyfikat już wygasł lub wkrótce wygaśnie. W takim przypadku ponów próbę, używając prawidłowego certyfikatu.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Zadanie 4 — Konfigurowanie systemu DNS w celu uzyskiwania dostępu do domeny zarządzanej z Internetu
> [!NOTE]
> **Opcjonalne zadania** — Jeśli nie ma dostępu do domeny zarządzanej przy użyciu LDAPS za pośrednictwem Internetu, pomiń to zadanie konfiguracji.
>
>

Przed rozpoczęciem tego zadania upewnij się, zostały wykonane kroki opisane w temacie [zadanie 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Po włączeniu bezpiecznego dostępu LDAP w Internecie dla domeny zarządzanej, musisz zaktualizować DNS tak, aby komputery klienckie można znaleźć tej domeny zarządzanej. Na koniec zadanie 3 zewnętrzny adres IP jest wyświetlany na **właściwości** karcie **IP adres dla LDAPS dostępu zewnętrznego**.

Tak, aby nazwa DNS domeny zarządzanej (na przykład "ldaps.contoso100.com") wskazuje to zewnętrzny adres IP, należy skonfigurować zewnętrznego dostawcy usługi DNS. Na przykład utworzyć następujący wpis DNS:

    ldaps.contoso100.com  -> 52.165.38.113

To wszystko — teraz można przystąpić do nawiązania połączenia domeny zarządzanej przy użyciu bezpiecznego protokołu LDAP, za pośrednictwem Internetu.

> [!WARNING]
> Należy pamiętać, że komputery klienckie muszą ufać wystawcy certyfikatu LDAPS, aby można było pomyślnie nawiązać połączenie przy użyciu LDAPS domeny zarządzanej. Jeśli używasz publicznie zaufany urząd certyfikacji, nie trzeba nic robić, ponieważ komputery klienckie ufać tych wystawców certyfikatów. Jeśli używasz certyfikatu z podpisem własnym, należy zainstalować części publicznego certyfikatu z podpisem własnym do magazynu zaufanych certyfikatów na komputerze klienckim.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Zadanie 5 blokowania bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu.
> [!NOTE]
> Jeśli nie włączono dostępu LDAPS do domeny zarządzanej za pośrednictwem Internetu, pomiń to zadanie konfiguracji.
>
>

Przed rozpoczęciem tego zadania upewnij się, zostały wykonane kroki opisane w temacie [zadanie 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Udostępnianie domeny zarządzanej LDAPS dostępu przez internet stanowi zagrożenie bezpieczeństwa. Domeny zarządzanej jest dostępny z Internetu na port używany do bezpiecznego protokołu LDAP (to znaczy port 636). W związku z tym można ograniczyć dostęp do domeny zarządzanej, aby określić adresy IP znane. Ze względów bezpieczeństwa należy utworzyć grupę zabezpieczeń sieci (NSG) i powiązać ją z podsieci, w której włączono usługi domenowe Azure AD.

W poniższej tabeli przedstawiono przykład grupy NSG można skonfigurować do blokowania bezpiecznego dostępu LDAP w Internecie. Grupa NSG zawiera zestaw reguł, które zapewnia przychodzących bezpieczny dostęp LDAP przez port TCP 636 tylko z określonego zestawu adresów IP. Domyślna reguła "DenyAll" dotyczy cały ruch przychodzący z Internetu. Reguły NSG umożliwiają LDAPS dostęp przez internet z określonych adresów IP ma wyższy priorytet niż reguły DenyAll NSG.

![Przykład grupy NSG bezpieczny dostęp do LDAPS w Internecie](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Więcej informacji** - [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md).

<br>


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy z połączeniem do domeny zarządzanej przy użyciu bezpiecznego protokołu LDAP, wykonaj następujące kroki:
* Upewnij się, że łańcucha wystawcy certyfikatu bezpiecznego LDAP jest zaufany na kliencie. Możesz dodać główny urząd certyfikacji do magazynu zaufanych certyfikatów głównych na kliencie, aby ustanowić relację zaufania.
* Sprawdź, czy klient LDAP (na przykład ldp.exe) nawiąże połączenie z bezpiecznego punktu końcowego protokołu LDAP, za pomocą nazwy DNS, a nie adres IP.
* Sprawdź nazwę DNS, który klient LDAP łączy się jest rozpoznawana jako publiczny adres IP z bezpiecznego protokołu LDAP do domeny zarządzanej.
* Sprawdź, czy bezpiecznego certyfikatu LDAP do domeny zarządzanej ma nazwę DNS w podmiot lub atrybut nazwy alternatywnej podmiotu.
* Jeśli jest nawiązywane za pośrednictwem bezpiecznego protokołu LDAP w Internecie, upewnij się, że ustawienia grupy NSG dla sieci wirtualnej zezwalać na ruch do portu 636 z Internetu.

Jeśli masz problemy z połączeniem do domeny zarządzanej przy użyciu bezpiecznego protokołu LDAP, [skontaktuj się z zespołem produktu](active-directory-ds-contact-us.md) Aby uzyskać pomoc. Należy uwzględnić następujące informacje, aby pomóc w zdiagnozowaniu problemu lepiej:
* Zrzut ekranu Ldp.exe połączenia i niepowodzenia.
* Identyfikator dzierżawy usługi Azure AD i nazwę domeny DNS domeny zarządzanej.
* Nazwa użytkownika dokładne, które próbujesz powiązać jako.


## <a name="related-content"></a>Zawartość pokrewna
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Administrowanie zasad grupy w domenie zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)
* [Utwórz grupę zabezpieczeń sieci](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

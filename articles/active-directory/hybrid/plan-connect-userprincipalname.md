---
title: Wypełnianie wartości UserPrincipalName w usłudze Azure AD
description: Ten dokument opisuje, jak atrybut UserPrincipalName jest wypełniony.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60382137"
---
# <a name="azure-ad-userprincipalname-population"></a>Wypełnianie wartości UserPrincipalName w usłudze Azure AD

W tym artykule opisano, jak atrybut UserPrincipalName jest wypełniana w usłudze Azure Active Directory (Azure AD).
Wartość atrybutu UserPrincipalName jest nazwa użytkownika usługi Azure AD dla kont użytkowników.

## <a name="upn-terminology"></a>Terminologia dotycząca nazwy UPN
Następującą terminologią jest używany w tym artykule:

|Termin|Opis|
|-----|-----|
|Domena początkowa|Domena domyślna (onmicrosoft.com) w dzierżawie usługi Azure AD. Na przykład "contoso.onmicrosoft.com".|
|Microsoft Online routingu Adres_e (MOERA)|Usługa Azure AD oblicza MOERA atrybut AD MailNickName platformy Azure i usługi Azure AD domeny początkowej jako &lt;MailNickName&gt;&#64;&lt;domeny początkowej&gt;.|
|Atrybut mailNickName w środowisku lokalnym|Atrybut w usłudze Active Directory, którego wartość reprezentuje alias użytkownika w organizacji programu Exchange.|
|Atrybut poczty w środowisku lokalnym|Atrybut w usłudze Active Directory, w których wartość reprezentuje adres e-mail użytkownika|
|Podstawowy adres SMTP|Adres podstawowy adres e-mail adresata obiektu programu Exchange. Na przykład SMTP:user\@contoso.com.|
|Identyfikatora logowania alternatywnej|W środowisku lokalnym atrybutu innego niż UserPrincipalName, takiego jak atrybut poczty, używane do logowania.|

## <a name="what-is-userprincipalname"></a>Co to jest UserPrincipalName?
UserPrincipalName jest atrybutu, który jest internetową nazwą logowania użytkownika na podstawie standardu Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Format nazwy UPN
Nazwy UPN składa się z prefiksu nazwy UPN (nazwa konta użytkownika) i sufiks głównej nazwy użytkownika (nazwy domeny DNS). Prefiks jest sprzężony z przy użyciu sufiksu "\@" symboli. Na przykład "ktoś\@example.com". Nazwy UPN musi być unikatowa wśród wszystkich obiektów podmiotów zabezpieczeń w obrębie lasu katalogu. 

## <a name="upn-in-azure-ad"></a>Nazwa UPN w usłudze Azure AD 
Nazwa UPN jest używany przez usługę Azure AD umożliwia użytkownikom logowanie.  Nazwy UPN, który użytkownik może używać, zależy od tego, czy domena została zweryfikowana.  Jeśli domeny została zweryfikowana, a następnie użytkownik mający ten sufiks będą mogli zalogować się do usługi Azure AD.  

Ten atrybut jest zsynchronizowany przy użyciu usługi Azure AD Connect.  Podczas instalacji można wyświetlić, domen, które zostały zweryfikowane i te, które nie mają.

   ![Niezweryfikowane domeny](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Identyfikatora logowania alternatywnej
W niektórych środowiskach użytkownicy końcowi mogą tylko należy pamiętać o swój adres e-mail i nie ich nazwy UPN.  Użyj adresu e-mail może być spowodowane zasady firmowe lub zależność line-of-business aplikacji w środowisku lokalnym.

Identyfikatora logowania alternatywnej pozwala na skonfigurowanie procesu logowania w którym użytkownicy mogą Zaloguj się przy użyciu atrybutu niż nazw UPN, taki jak adres e-mail.

Aby włączyć Identyfikatora logowania alternatywny z usługą Azure AD, żadne kroki dodatkowe konfiguracje są wymagane, gdy za pomocą usługi Azure AD Connect. Alternatywny identyfikator można skonfigurować bezpośrednio z kreatora. Zobacz konfiguracji usługi Azure AD logowania dla użytkowników w sekcji synchronizacja. W obszarze **główna nazwa użytkownika** listę rozwijaną, wybierz atrybut dla identyfikatora logowania alternatywnej.

![Niezweryfikowane domeny](./media/plan-connect-userprincipalname/altloginid.png)  

Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) i [Konfiguracja logowania w usłudze Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufiks głównej nazwy użytkownika z innego niż zweryfikowane
Jeśli sufiks Identyfikatora logowania atrybutu/alternatywny UserPrincipalName w środowisku lokalnym nie jest weryfikowany za pomocą dzierżawy usługi Azure AD, aby MOERA jest ustawić wartość atrybutu UserPrincipalName usługi Azure AD. Usługa Azure AD oblicza MOERA atrybut Azure AD MailNickName i usługi Azure AD domeny początkowej jako &lt;MailNickName&gt;&#64;&lt;domeny początkowej&gt;.

## <a name="verified-upn-suffix"></a>Zweryfikowano sufiks głównej nazwy użytkownika
Jeśli atrybut UserPrincipalName lokalnych/alternatywny sufiks Identyfikatora logowania jest weryfikowany za pomocą dzierżawy usługi Azure AD, a następnie wartość atrybutu UserPrincipalName usługi Azure AD będzie taka sama jak wartość Identyfikatora logowania atrybutu/alternatywny UserPrincipalName w środowisku lokalnym.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Obliczanie wartości atrybutu w usłudze Azure AD MailNickName
Ponieważ MOERA może być równa wartości atrybutu UserPrincipalName usługi Azure AD, jest ważne, aby zrozumieć, jak jest obliczana wartość atrybutu Azure AD MailNickName, czyli prefiks MOERA.

Podczas synchronizowania obiektu użytkownika do dzierżawy usługi Azure AD po raz pierwszy usługi Azure AD sprawdza następujące elementy w podanej kolejności i ustawia wartość atrybutu MailNickName z pierwszym istniejące:

- Atrybut mailNickName w środowisku lokalnym
- Prefiks podstawowy adres SMTP
- Prefiks atrybutu poczty w środowisku lokalnym
- Prefiks Identyfikatora logowania alternatywny/atrybut userPrincipalName w środowisku lokalnym
- Prefiks adresu SMTP użytkownika dodatkowej

Aktualizacje do obiektu użytkownika są synchronizowane z dzierżawą usługi AD Azure, usługi Azure AD aktualizuje wartość atrybutu MailNickName tylko w przypadku, gdy jest dostępna aktualizacja, wartość atrybutu mailNickName w środowisku lokalnym.

>[!IMPORTANT]
>Ponownie oblicza wartość atrybutu UserPrincipalName usługi Azure AD, tylko w przypadku, gdy aktualizacja wartości Identyfikatora logowania atrybutu/alternatywny UserPrincipalName w środowisku lokalnym jest synchronizowane z dzierżawy usługi Azure AD. 
>
>Zawsze, gdy usługa Azure AD ponownie oblicza atrybut UserPrincipalName, oblicza również ponownie MOERA. 

## <a name="upn-scenarios"></a>Scenariusze nazwy UPN
Poniżej przedstawiono przykładowe scenariusze dotyczące sposobu nazwa UPN jest obliczany na podstawie danego scenariusza.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenariusz 1: Zweryfikowano inne niż sufiks nazwy UPN — początkowej synchronizacji

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Obiekt użytkownika w środowisku lokalnym:
- mailNickName: &lt;nieustawiona&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- wiadomości e-mail: us2@contoso.com
- userPrincipalName: us3@contoso.com"

Synchronizowane obiektu użytkownika do dzierżawy usługi Azure AD po raz pierwszy
- Atrybut usługi Azure AD MailNickName na podstawowym prefiksu adresu SMTP.
- Ustaw MOERA &lt;MailNickName&gt;&#64;&lt;domeny początkowej&gt;.
- Atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi AD platformy Azure:
- MailNickName      : us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenariusz 2: Zweryfikowano bez sufiksu UPN — zestawu w środowisku lokalnym atrybut mailNickName

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Obiekt użytkownika w środowisku lokalnym:
- mailNickName      : us4
- proxyAddresses: {SMTP:us1@contoso.com}
- wiadomości e-mail: us2@contoso.com
- UserPrincipalName: us3@contoso.com

Synchronizowanie aktualizacji na atrybut mailNickName środowiska lokalnego do dzierżawy usługi Azure AD
- Aktualizacja Azure AD MailNickName atrybut o atrybut mailNickName w środowisku lokalnym.
- Ponieważ nie ma aktualizacji atrybut userPrincipalName w środowisku lokalnym, nie została zmieniona z atrybutem UserPrincipalName usługi Azure AD.

Obiekt użytkownika dzierżawy usługi AD platformy Azure:
- MailNickName      : us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenariusz 3: Zweryfikowano bez sufiksu UPN — aktualizacja lokalnych atrybut userPrincipalName

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Obiekt użytkownika w środowisku lokalnym:
- mailNickName      : us4
- proxyAddresses: {SMTP:us1@contoso.com}
- wiadomości e-mail: us2@contoso.com
- UserPrincipalName: us5@contoso.com

Synchronizowanie aktualizacji na atrybut userPrincipalName środowiska lokalnego do dzierżawy usługi Azure AD
- Aktualizacja lokalnych atrybut userPrincipalName wyzwala ponowne obliczanie atrybutu MOERA i UserPrincipalName usługi Azure AD.
- Ustaw MOERA &lt;MailNickName&gt;&#64;&lt;domeny początkowej&gt;.
- Atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi AD platformy Azure:
- MailNickName      : us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenariusz 4: Zweryfikowane inne niż sufiks nazwy UPN — aktualizacja podstawowego adresu SMTP użytkownika, jak i lokalnych poczty atrybutu

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Obiekt użytkownika w środowisku lokalnym:
- mailNickName      : us4
- proxyAddresses: {SMTP:us6@contoso.com}
- wiadomości e-mail: us7@contoso.com
- UserPrincipalName: us5@contoso.com

Synchronizowanie aktualizacji na atrybut poczty w środowisku lokalnym i podstawowego adresu SMTP użytkownika do dzierżawy usługi Azure AD
- Po początkowej synchronizacji obiektu użytkownika aktualizacji do lokalnej poczty atrybutu i podstawowy adres SMTP nie wpłynie na AD MailNickName platformy Azure lub atrybutu UserPrincipalName.

Obiekt użytkownika dzierżawy usługi AD platformy Azure:
- MailNickName      : us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenariusz 5: Zweryfikowano sufiks nazwy UPN — aktualizacja lokalnych sufiks domeny atrybutu userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Obiekt użytkownika w środowisku lokalnym:
- mailNickName      : us4
- proxyAddresses: {SMTP:us6@contoso.com}
- wiadomości e-mail: us7@contoso.com
- UserPrincipalName: us5@verified.contoso.com

Synchronizowanie aktualizacji na atrybut userPrincipalName środowiska lokalnego do dzierżawy usługi Azure AD
- Aktualizacja ponowne obliczenie wyzwalaczy atrybut userPrincipalName lokalną atrybutu UserPrincipalName usługi Azure AD.
- Atrybut UserPrincipalName usługi Azure AD na lokalnego atrybut userPrincipalName jako sufiks głównej nazwy użytkownika została zweryfikowana za pomocą dzierżawy usługi Azure AD.

Obiekt użytkownika dzierżawy usługi AD platformy Azure:
- MailNickName      : us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>Następne kroki
- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)

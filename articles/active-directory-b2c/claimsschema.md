---
title: ClaimsSchema — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element ClaimsSchema zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5d7036f2c7301223b27c80402dace8e9ea05b7f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487825"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema** element definiuje typy oświadczeń, które mogą być przywoływane w ramach zasad. Schemat oświadczeń jest miejscem, gdzie możesz deklarować swoje oświadczenia. Oświadczenia mogą być imię, ostatni nazwa, wyświetlana nazwa, numer telefonu i więcej. Lista zawiera ClaimsSchema element **oświadczenia** elementów. **Oświadczenia** element zawiera **identyfikator** atrybut, który nazywa się oświadczenia. 

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>Typ oświadczenia

**Oświadczenia** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który jest używany dla typu oświadczenia. Inne elementy, można użyć tego identyfikatora w zasadach. |

**Oświadczenia** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Nazwa wyświetlana | 0:1 | Tytuł, który jest widoczny dla użytkowników na różnych ekranach. Wartość może być [zlokalizowane](localization.md). |
| DataType | 0:1 | Typ oświadczenia. Typy danych atrybut typu wartość logiczna, date, dateTime, int, long, string, stringCollection, alternativeSecurityIdCollection mogą być używane. |
| DefaultPartnerClaimTypes | 0:1 | Domyślne partnera Przejmij typów do użycia dla określonego protokołu. Wartość może zostać zastąpiona w **PartnerClaimType** określonych w **oświadczenie InputClaim** lub **oświadczenie OutputClaim** elementów. Ten element umożliwia określenie domyślnej nazwy dla protokołu.  |
| Maska | 0:1 | Opcjonalny ciąg znaków, które mogą być stosowane podczas wyświetlania oświadczenie maskowania. Na przykład 324-232-4343 numerów telefonów mogą być niewidoczne jako 4343-XXX-XXX. |
| UserHelpText | 0:1 | Opis typu oświadczenia, które mogą być przydatne dla użytkowników zrozumieć jej cel. Wartość może być [zlokalizowane](localization.md). |
| UserInputType | 0:1 | Typ kontrolki wprowadzania, który ma być dostępny dla użytkownika, gdy ręcznego wprowadzania danych oświadczenia dla typu oświadczenia. Zobacz typy wejściowe użytkownika zdefiniowane w dalszej części tej strony. |
| Ograniczenie | 0:1 | Ograniczenia dotyczące wartości dla tego oświadczenia, takie jak wyrażenie regularne (Regex) lub Podaj listę dopuszczalnymi wartościami. Wartość może być [zlokalizowane](localization.md). |
PredicateValidationReference| 0:1 | Odwołanie do **PredicateValidationsInput** elementu. **PredicateValidationReference** elementy umożliwiają wykonywanie procesu weryfikacji, aby upewnić się, tylko poprawnie sformułowany danych wprowadzonych. Aby uzyskać więcej informacji, zobacz [predykaty](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** może zawierać następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Protokół | 0: n | Listę protokołów z ich domyślne partnera Przejmij Nazwa typu. |

**Protokołu** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Yes | Nazwa Nieprawidłowa protokołu obsługiwanego przez usługę Azure AD B2C. Możliwe wartości:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed lub WsTrust. |
| PartnerClaimType | Yes | Nazwa typu oświadczenia, która ma być używany. |

W poniższym przykładzie, gdy struktura środowiska tożsamości korzysta z dostawcy tożsamości SAML2 lub aplikacji jednostki uzależnionej **nazwisko** oświadczeń jest mapowany na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, za pomocą OpenIdConnect i protokołu OAuth2 jest oświadczenie mapowany do `family_name`.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

W wyniku token JWT wystawione przez usługę Azure AD B2C, emituje `family_name` zamiast nazwy typu oświadczenia **nazwisko**.
 
```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maska

**Maski** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Yes | Typ oświadczenia maski. Możliwe wartości: `Simple` lub `Regex`. `Simple` Wartość wskazuje, że maska prosty tekst jest stosowane wiodących części oświadczenia ciągu. `Regex` Wartość wskazuje, że wyrażenie regularne jest stosowany do oświadczenia ciągu jako całości.  Jeśli `Regex` jest określona, opcjonalny atrybut musi także być zdefiniowany za pomocą wyrażeń regularnych do użycia. |
| `Regex` | Nie | Jeśli **`Type`** ustawiono `Regex`, określ wyrażenie regularne do użycia.

Poniższy przykład umożliwia skonfigurowanie **PhoneNumber** oświadczenia `Simple` maski:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Struktura środowiska tożsamości renderuje numeru telefonu podczas ukrywanie pierwsze sześć cyfr:

![Za pomocą oświadczenia typu, za pomocą maski](./media/claimsschema/mask.png)

Poniższy przykład umożliwia skonfigurowanie **AlternateEmail** oświadczenia `Regex` maski:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Struktura środowiska tożsamości powoduje wyświetlenie tylko pierwszą literę adresu e-mail i nazwy domeny poczty e-mail:

![Za pomocą oświadczenia typu, za pomocą maski](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Ograniczenie

**Ograniczeń** element może zawierać następującego atrybutu:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MergeBehavior | Nie | Metoda użyta do scalenia wartości wyliczenia z oświadczenia w zasadach nadrzędnego zawierających ten sam identyfikator. Podczas zastępowania oświadczenia określone w zasadach podstawowych, należy użyć tego atrybutu. Możliwe wartości: `Append`, `Prepend`, lub `ReplaceAll`. `Append` Wartość jest kolekcją danych, który ma zostać dodany na końcu kolekcji określonej w zasadach nadrzędnej. `Prepend` Wartość jest kolekcją danych, który ma zostać dodany przed kolekcji określonej w zasadach nadrzędnej. `ReplaceAll` Wartość jest kolekcją danych, określonym w zasadach nadrzędnego, które mają być ignorowane. |

**Ograniczeń** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Wyliczenie | 1: n | Dostępne opcje w interfejsie użytkownika dla użytkownika wybrać do roszczenia, takie jak wartości na liście rozwijanej. |
| Wzorce | 1:1 | Wyrażenie regularne do użycia. |

### <a name="enumeration"></a>Wyliczenie

**Wyliczenie** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Tekst | Yes | Ciąg wyświetlany, który jest wyświetlany w interfejsie użytkownika dla tej opcji. |
|Wartość | Yes | Wartość oświadczenia, który jest skojarzony z wybraniu tej opcji. |
| SelectByDefault | Nie | Wskazuje, czy należy wybrać tę opcję, domyślnie w interfejsie użytkownika. Możliwe wartości: Wartość TRUE lub False. |

Poniższy przykład umożliwia skonfigurowanie **Miasto** lista rozwijana lista oświadczeń z wartością domyślną wartość `New York`:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```
Lista rozwijana lista miasta z wartością domyślną, ustaw Nowy Jork:

![Lista rozwijana Miasto listy](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Wzorce

**Wzorzec** element może zawierać następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| RegularExpression | Yes | Wyrażenie regularne oświadczeń tego typu muszą być zgodne, aby był prawidłowy. |
| HelpText | Nie | Wzorzec lub wyrażenie regularne dla tego oświadczenia. |

Poniższy przykład umożliwia skonfigurowanie **e-mail** oświadczenia za pomocą wyrażeń regularnych wejściowy weryfikacji i tekst pomocy:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Struktura środowiska tożsamości renderuje oświadczenia adresu e-mail dzięki walidacji danych wejściowych w formacie wiadomości e-mail:

![Za pomocą oświadczenia typu, ze wzorcem](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Usługa Azure AD B2C obsługuje wiele typów danych wejściowych użytkownika, takich jak pole tekstowe, hasło i listę rozwijaną listę, która może być używany podczas ręcznego wprowadzania danych oświadczenia dla typu oświadczenia. Należy określić **UserInputType** podczas zbierania informacji od użytkownika przy użyciu [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

**TextBox** typ danych wejściowych użytkownika służy do zapewnienia pole tekstowe w jeden wiersz.

![Za pomocą oświadczenie typu za pomocą textbox](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

**EmailBox** typ danych wejściowych użytkownika jest używana, aby zapewnić pole wejściowe podstawowy adres e-mail.

![Za pomocą oświadczenia typu, przy użyciu emailbox](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>Hasło

**Hasło** typ danych wejściowych użytkownika jest używana do rejestrowania hasła podanego przez użytkownika.

![Za pomocą oświadczenia typu, przy użyciu hasła](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

**DateTimeDropdown** typ danych wejściowych użytkownika służy do zapewnienia zbiór rozwijanych, aby wybrać dzień, miesiąc i rok. Predykaty i PredicateValidations elementów można użyć do kontrolowania wartości minimalne i maksymalne daty. Aby uzyskać więcej informacji, zobacz **skonfigurować zakres dat** części [predykatów i PredicateValidations](predicates.md).

![Za pomocą oświadczenia typu, przy użyciu datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** typ danych wejściowych użytkownika służy do zapewnienia kolekcję przycisków radiowych, który umożliwia użytkownikowi wybranie jednej opcji.

![Za pomocą oświadczenia typu, przy użyciu radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>    
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

**DropdownSingleSelect** typ danych wejściowych użytkownika służy do zapewnienia pole listy rozwijanej, która umożliwia użytkownikowi wybranie jednej opcji.

![Za pomocą oświadczenia typu, przy użyciu dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

**CheckboxMultiSelect** typ danych wejściowych użytkownika służy do zapewnienia zbiór pól wyboru, która umożliwia użytkownikowi wybranie wielu opcji.

![Za pomocą oświadczenia typu, przy użyciu checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>tylko do odczytu

**Tylko do odczytu** typ danych wejściowych użytkownika służy do zapewnienia pola tylko do odczytu, aby wyświetlić oświadczenia i wartości.

![Za pomocą oświadczenia typu, przy użyciu tylko do odczytu](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Akapit

**Akapitu** typ danych wejściowych użytkownika służy do zapewnienia pola, które zawiera tekst tylko w tagu akapitu. Na przykład &lt;p&gt;tekstu&lt;/p&gt;.

![Za pomocą oświadczenia typu, przy użyciu akapitu](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Aby wyświetlić jeden z **wyliczenie** wartości w **responseMsg** roszczenie, należy użyć `GetMappedValueFromLocalizedCollection` lub `CreateStringClaim` przekształcania oświadczeń. Aby uzyskać więcej informacji, zobacz [przekształcenia oświadczeń ciągu](string-transformations.md) 

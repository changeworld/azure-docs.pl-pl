---
title: ClaimsSchema — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Określ element ClaimsSchema zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78397805"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Element ClaimsSchema** definiuje typy oświadczeń, do których można się odwoływać jako część zasad. Schemat oświadczeń jest miejscem, w którym deklarujesz swoje oświadczenia. Roszczenie może być imię, nazwisko, imię, imię, numer telefonu i więcej. Element ClaimsSchema zawiera listę elementów **ClaimType.** Element **ClaimType** zawiera atrybut **Id,** który jest nazwą oświadczenia.

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

## <a name="claimtype"></a>Claimtype

Element **ClaimType** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany dla typu oświadczenia. Inne elementy można użyć tego identyfikatora w zasadach. |

Element **ClaimType** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Tytuł wyświetlany użytkownikom na różnych ekranach. Wartość może być [zlokalizowana](localization.md). |
| typ_danych | 1:1 | Rodzaj oświadczenia. |
| Domyślne typy partnerów | 0:1 | Domyślne typy oświadczeń partnera do użycia dla określonego protokołu. Wartość może zostać zastąpiona w **PartnerClaimType** określony w **InputClaim** lub **OutputClaim** elementów. Ten element służy do określania domyślnej nazwy protokołu.  |
| Maska | 0:1 | Opcjonalny ciąg znaków maskujących, które mogą być stosowane podczas wyświetlania oświadczenia. Na przykład numer telefonu 324-232-4343 można zamaskować jako XXX-XXX-4343. |
| UserHelpText (Tekst pomocy użytkownika) | 0:1 | Opis typu oświadczenia, który może być pomocny dla użytkowników, aby zrozumieć jego cel. Wartość może być [zlokalizowana](localization.md). |
| Typ użytkownika | 0:1 | Typ formantu wejściowego, który powinien być dostępny dla użytkownika podczas ręcznego wprowadzania danych oświadczeń dla typu oświadczenia. Zobacz typy danych wejściowych użytkownika zdefiniowane w dalszej części tej strony. |
| AdminHelpText (Pomoc administracyjna) | 0:1 | Opis typu oświadczenia, który może być przydatny dla administratorów, aby zrozumieć jego cel. |
| Ograniczenie | 0:1 | Ograniczenia wartości dla tego oświadczenia, takie jak wyrażenie regularne (Regex) lub lista dopuszczalnych wartości. Wartość może być [zlokalizowana](localization.md). |
PredicateValidationReference| 0:1 | Odwołanie do **PredicateValidationsInput** elementu. **PredicateValidationReference** elementy umożliwiają wykonywanie procesu sprawdzania poprawności, aby upewnić się, że tylko poprawnie utworzone dane są wprowadzane. Aby uzyskać więcej informacji, zobacz [Predykaty](predicates.md). |



### <a name="datatype"></a>typ_danych

Element **DataType** obsługuje następujące wartości:

| Typ | Opis |
| ------- | ----------- |
|wartość logiczna|Reprezentuje wartość logiczną (`true` lub `false`).|
|date| Reprezentuje chwilę w czasie, zwykle wyrażoną jako data dnia. Wartość daty jest zgodna z konwencją ISO 8601.|
|Data i godzina|Reprezentuje chwilę w czasie, zwykle wyrażoną jako data i godzina dnia. Wartość daty jest zgodna z konwencją ISO 8601.|
|czas trwania|Reprezentuje przedział czasu w latach, miesiącach, dniach, godzinach, minutach i sekundach. Format jest `PnYnMnDTnHnMnS`, `P` gdzie wskazuje wartość `N` dodatnią lub ujemną. `nY`to liczba lat, po których `Y`następuje dosłowny . `nMo`to liczba miesięcy, po których `Mo`następuje dosłowny . `nD`to liczba dni, po których `D`następuje literał . Przykłady: `P21Y` reprezentuje 21 lat. `P1Y2Mo`wynosi jeden rok i dwa miesiące. `P1Y2Mo5D`wynosi jeden rok, dwa miesiące i pięć dni.  `P1Y2M5DT8H5M620S`oznacza jeden rok, dwa miesiące, pięć dni, osiem godzin, pięć minut i dwadzieścia sekund.  |
|phoneNumber|Reprezentuje numer telefonu. |
|int| Reprezentuje liczbę między -2,147,483,648 i 2,147,483,647|
|long| Reprezentuje liczbę między -9,223,372,036,854,775,808 do 9,223,372,036,854,775,807 |
|ciąg| Reprezentuje tekst jako sekwencję jednostek kodu UTF-16.|
|Stringcollection|Reprezentuje kolekcję `string`.|
|użytkownikTożowicieli| Reprezentuje tożsamość użytkownika.|
|użytkownikIdentityCollection|Reprezentuje kolekcję `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>Domyślne typy partnerów

**DefaultPartnerClaimTypes** może zawierać następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Protocol (Protokół) | 1:n | Lista protokołów z domyślną nazwą typu oświadczenia partnera. |

Element **Protokołu** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez usługę Azure AD B2C. Możliwe wartości to: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| Typ partnera | Tak | Nazwa typu oświadczenia, która ma być używana. |

W poniższym przykładzie, gdy struktura środowiska tożsamości wchodzi w interakcję z dostawcą tożsamości SAML2 lub aplikacją jednostki uzależnionej, oświadczenie **nazwiska** jest mapowane na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, z OpenIdConnect i OAuth2, oświadczenie jest mapowane na `family_name`.

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

W rezultacie token JWT wystawiony przez usługę Azure AD `family_name` B2C emituje **nazwę**nazwy zamiast ClaimType .

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

Element **Mask** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ maski oświadczenia. Możliwe `Simple` wartości: `Regex`lub . Wartość `Simple` wskazuje, że prosta maska tekstowa jest stosowana do wiodącej części oświadczenia ciągu. Wartość `Regex` wskazuje, że wyrażenie regularne jest stosowane do oświadczenia ciągu jako całości.  Jeśli `Regex` wartość jest określona, atrybut opcjonalny musi być również zdefiniowany z wyrażeniem regularnym do użycia. |
| `Regex` | Nie | Jeśli **`Type`** jest `Regex`ustawiona na , określ wyrażenie regularne do użycia.

W poniższym przykładzie konfiguruje oświadczenie `Simple` **phonenumber** z maską:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Struktura środowiska tożsamości renderuje numer telefonu, ukrywając pierwsze sześć cyfr:

![Roszczenie numer telefonu wyświetlane w przeglądarce z pierwszych sześciu cyfr maskowane przez Xs](./media/claimsschema/mask.png)

W poniższym przykładzie konfiguruje `Regex` oświadczenie **AlternateEmail** z maską:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Struktura środowiska tożsamości renderuje tylko pierwszą literę adresu e-mail i nazwę domeny e-mail:

![Oświadczenie e-mail wyświetlane w przeglądarce ze znakami zamaskowanych przez gwiazdki](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Ograniczenie

Element **Ograniczenie** może zawierać następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| MergeBehavior (Zachowanie scalania) | Nie | Metoda używana do scalania wartości wyliczenia z ClaimType w zasadach nadrzędnych o tym samym identyfikatorze. Użyj tego atrybutu podczas zastępowanie oświadczenia określonego w zasadach podstawowych. Możliwe `Append`wartości: `Prepend`, `ReplaceAll`, lub . Wartość `Append` jest kolekcją danych, które powinny być dołączane na końcu kolekcji określonej w zasadach nadrzędnych. Wartość `Prepend` jest kolekcją danych, które powinny zostać dodane przed kolekcją określoną w zasadach nadrzędnych. Wartość `ReplaceAll` jest zbiorem danych określonych w zasadach nadrzędnych, które powinny być ignorowane. |

**Element Restriction** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wyliczenie | 1:n | Dostępne opcje w interfejsie użytkownika dla użytkownika, aby wybrać dla oświadczenia, takie jak wartość w rozwijanej. |
| Wzorce | 1:1 | Wyrażenie regularne do użycia. |

#### <a name="enumeration"></a>Wyliczenie

Element **Wyliczenia** definiuje dostępne opcje dla użytkownika, aby wybrać dla oświadczenia w interfejsie użytkownika, takie jak wartość w `CheckboxMultiSelect`, `DropdownSingleSelect`, lub `RadioSingleSelect`. Alternatywnie można zdefiniować i zlokalizować dostępne opcje za pomocą [localizedCollections](localization.md#localizedcollections) elementu. Aby wyszukać element z kolekcji **wyliczenia** oświadczenia, należy użyć przekształcenia oświadczeń [GetMappedValueFromLocalizedCollection.](string-transformations.md#getmappedvaluefromlocalizedcollection)

Element **wyliczenia** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Tekst | Tak | Ciąg wyświetlania, który jest wyświetlany użytkownikowi w interfejsie użytkownika dla tej opcji. |
|Wartość | Tak | Wartość oświadczenia skojarzona z wybraniem tej opcji. |
| Wybierz opcjęObdefault | Nie | Wskazuje, czy ta opcja powinna być zaznaczona domyślnie w interfejsie użytkownika. Możliwe wartości: Prawda lub Fałsz. |

W poniższym przykładzie skonfigurowano oświadczenie listy rozwijanej `New York` **miasta** z domyślną wartością ustawioną na:

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

Lista miast rozwijanej z wartością domyślną ustawioną na Nowy Jork:

![Kontrolka rozwijana renderowana w przeglądarce i wyświetlana wartość domyślna](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Wzorce

Element **Wzorzec** może zawierać następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Regularnewygasijenie | Tak | Wyrażenie regularne, że oświadczenia tego typu muszą być zgodne, aby były prawidłowe. |
| Helptext | Nie | Komunikat o błędzie dla użytkowników, jeśli sprawdzanie wyrażeń regularnych nie powiedzie się. |

W poniższym przykładzie skonfigurowano oświadczenie **e-mail** z weryfikacją danych wejściowych wyrażenia regularnego i tekstem pomocy:

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

Struktura środowiska tożsamości renderuje oświadczenie adresu e-mail z weryfikacją danych wejściowych formatu poczty e-mail:

![TextBox z komunikatem o błędzie wyzwalanym przez ograniczenie wyrażenia regularnego](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>Typ użytkownika

Usługa Azure AD B2C obsługuje różne typy danych wejściowych użytkownika, takie jak pole tekstowe, hasło i lista rozwijana, które mogą być używane podczas ręcznego wprowadzania danych oświadczeń dla typu oświadczenia. Należy określić **UserInputType** podczas zbierania informacji od użytkownika przy użyciu [samodzielnie potwierdzonego profilu technicznego](self-asserted-technical-profile.md) i [formantów wyświetlania](display-controls.md).

Element **UserInputType** dostępne typy danych wejściowych użytkownika:

| Typ użytkownika | Obsługiwany typ roszczenia | Opis |
| --------- | -------- | ----------- |
|Pole wyboruMultiSelect| `string` |Pole rozwijane wielokrotnego wyboru. Wartość oświadczenia jest reprezentowana w ciągu ogranicznika przecinka wybranych wartości. |
|DateTimeDropdown | `date`, `dateTime` |Listy rozwijane, aby wybrać dzień, miesiąc i rok. |
|Rozwijaneleselektuj |`string` |Pojedyncze pole listy rozwijanej wyboru. Wartość oświadczenia jest wybraną wartością.|
|Skrzynka e-mail | `string` |Pole wprowadzania wiadomości e-mail. |
|Akapitu | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Pole, które pokazuje tekst tylko w znaczniku akapitu. |
|Hasło | `string` |Pole tekstowe hasła.|
|RadioSingleSelect |`string` | Kolekcja przycisków radiowych. Wartość oświadczenia jest wybraną wartością.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Pole tekstowe tylko do odczytu. |
|TextBox |`boolean`, `int`, `string` |Jednowierszowe pole tekstowe. |


#### <a name="textbox"></a>TextBox

Typ wprowadzania danych użytkownika **TextBox** jest używany do zapewnienia jednowierszowego pola tekstowego.

![TextBox z właściwościami określonymi w typie oświadczenia](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>Skrzynka e-mail

Typ wprowadzania danych użytkownika **EmailBox** służy do zapewnienia podstawowego pola wprowadzania wiadomości e-mail.

![EmailBox z właściwościami określonymi w typie oświadczenia](./media/claimsschema/emailbox.png)

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

#### <a name="password"></a>Hasło

Typ **wprowadzania** hasła użytkownika służy do rejestrowania hasła wprowadzonego przez użytkownika.

![Używanie typu oświadczenia z hasłem](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Typ wejściowy użytkownika **DateTimeDropdown** służy do zapewnienia zestawu rozwijanych, aby wybrać dzień, miesiąc i rok. Można użyć predykatów i PredicateValidations elementów do kontrolowania wartości daty minimalnej i maksymalnej. Aby uzyskać więcej informacji, zobacz **sekcję Konfigurowanie zakresu dat** [w predykatach i predykacjach](predicates.md).

![Używanie typu oświadczenia z datą w dół](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Typ danych wejściowych użytkownika **RadioSingleSelect** służy do zapewnienia kolekcji przycisków radiowych, które umożliwiają użytkownikowi wybranie jednej opcji.

![Używanie typu oświadczenia z radiodsingleselect](./media/claimsschema/radiosingleselect.png)

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

#### <a name="dropdownsingleselect"></a>Rozwijaneleselektuj

Typ danych wejściowych użytkownika **RozwijanySingleSelect** służy do udostępnienia pola rozwijanego, które umożliwia użytkownikowi wybranie jednej opcji.

![Używanie typu oświadczenia z rozwijanymwysiewu](./media/claimsschema/dropdownsingleselect.png)

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

#### <a name="checkboxmultiselect"></a>Pole wyboruMultiSelect

Typ danych wejściowych **checkboxMultiSelect** użytkownika służy do zapewnienia kolekcji pól wyboru, które umożliwiają użytkownikowi wybranie wielu opcji.

![Używanie typu oświadczenia z polem wyborumultiselect](./media/claimsschema/checkboxmultiselect.png)

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

#### <a name="readonly"></a>Readonly

Typ danych wejściowych użytkownika tylko do **odczytu** służy do dostarczania pola tylko do odczytu w celu wyświetlenia oświadczenia i wartości.

![Używanie typu oświadczenia z tylko do odczytu](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Akapitu

Typ wprowadzania użytkownika **Akapit** jest używany do dostarczania pola, które pokazuje tekst tylko w znaczniku akapitu.  Na przykład &lt;&gt;p&lt;tekst&gt;/p . A **A** Paragraph `OutputClaim` user input type of self-asserted technical profile, must set the `Required` attribute `false` (default).

![Używanie typu oświadczenia z akapitem](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

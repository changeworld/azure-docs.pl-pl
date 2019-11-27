---
title: Konfigurowanie funkcji zapisywania zwrotnego haseł dla SSPR — Azure Active Directory
description: Używanie usługi Azure AD i Azure AD Connect do zapisywania zwrotnego haseł w katalogu lokalnym
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acda877ecadc8ad0abd09b78d5453743e2470b1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381154"
---
# <a name="how-to-configure-password-writeback"></a>Instrukcje: Konfigurowanie zapisywania zwrotnego haseł

W poniższych krokach przyjęto założenie, że Azure AD Connect w środowisku został już skonfigurowany przy użyciu ustawień [ekspresowych](../hybrid/how-to-connect-install-express.md) lub [niestandardowych](../hybrid/how-to-connect-install-custom.md) .

1. Aby skonfigurować i włączyć zapisywanie zwrotne haseł, zaloguj się na serwerze programu Azure AD Connect i uruchom kreatora konfiguracji programu **Azure AD Connect**.
2. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
3. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
4. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **Dalej**.
5. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
6. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.
   ![Włącz funkcję zapisywania zwrotnego haseł w Azure AD Connect][Writeback]
7. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

Typowe zadania związane z rozwiązywaniem problemów dotyczące funkcji zapisywania zwrotnego haseł znajdują się w sekcji [Rozwiązywanie problemów z funkcją zapisywania zwrotnego haseł](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) w naszym artykule

> [!WARNING]
> Zapisywanie zwrotne haseł przestanie działać dla klientów korzystających z Azure AD Connect wersji 1.0.8641.0 i starszych, gdy [usługa Azure Access Control Service (ACS) zostanie wycofana w dniu 7 listopada, 2018](../develop/active-directory-acs-migration.md). Azure AD Connect wersje 1.0.8641.0 i starsze nie będą już zezwalały na zapisywanie zwrotne haseł, ponieważ zależą one od usług ACS dla tej funkcji.
>
> Aby uniknąć przerw w działaniu usługi, należy uaktualnić poprzednią wersję Azure AD Connect do nowszej wersji, zapoznaj się z artykułem [Azure AD Connect: uaktualnienie z poprzedniej wersji do najnowszej](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania dotyczące licencjonowania dla zapisywania zwrotnego haseł

**Samoobsługowe resetowanie/zmiana/odblokowanie hasła przy użyciu lokalnego zapisywania zwrotnego to funkcja Premium usługi Azure AD**. Aby uzyskać więcej informacji o licencjonowaniu, zobacz [witrynę Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

Aby korzystać z funkcji zapisywania zwrotnego haseł, musisz mieć jedną z następujących licencji przypisanych do dzierżawy:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Enterprise Mobility + Security E3 lub A3
* Enterprise Mobility + Security E5 lub A5
* Microsoft 365 E3 lub A3
* Microsoft 365 E5 lub A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Autonomiczne plany licencjonowania pakietu Office 365 *nie obsługują funkcji samoobsługowego resetowania hasła/zmiany/odblokowywania przy użyciu lokalnego zapisywania zwrotnego* i wymagają posiadania jednego z powyższych planów, aby ta funkcja działała.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Uprawnienia Active Directory i lokalne zasady złożoności haseł 

Konto określone w narzędziu Azure AD Connect musi mieć ustawione następujące elementy, jeśli chcesz mieć zakres dla SSPR:

* **Resetowanie hasła** 
* **Zmień hasło** 
* **Uprawnienia do zapisu** w `lockoutTime`
* **Uprawnienia do zapisu** w `pwdLastSet`
* **Rozszerzone prawa** na:
   * Obiekt główny *każdej domeny* w tym lesie
   * Jednostki organizacyjne (OU) użytkownika, które mają być objęte zakresem dla SSPR

Jeśli nie masz pewności, do którego konta odwołuje się podane konto, Otwórz interfejs użytkownika konfiguracji Azure Active Directory Connect i wybierz opcję **Wyświetl bieżącą konfigurację** . Konto, do którego należy dodać uprawnienie, znajduje się na liście **synchronizowanych katalogów**.

Jeśli ustawisz te uprawnienia, konto usługi MA dla każdego lasu może zarządzać hasłami w imieniu kont użytkowników w tym lesie. 

> [!IMPORTANT]
> Jeśli nie Poprzednia przypiszesz tych uprawnień, mimo że zapisywanie zwrotne zostanie prawidłowo skonfigurowane, użytkownicy będą napotykać błędy podczas próby zarządzania hasłami lokalnymi w chmurze.
>

> [!NOTE]
> Replikowanie tych uprawnień do wszystkich obiektów w katalogu może potrwać do godziny lub dłużej.
>

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego haseł, wykonaj następujące czynności:

1. Otwórz Active Directory Użytkownicy i komputery przy użyciu konta, które ma odpowiednie uprawnienia do administrowania domeną.
2. W menu **Widok** upewnij się, że **funkcje zaawansowane** są włączone.
3. W lewym panelu kliknij prawym przyciskiem myszy obiekt, który reprezentuje katalog główny domeny, a następnie wybierz polecenie **właściwości** > **zabezpieczenia** > **Zaawansowane**.
4. Na karcie **uprawnienia** wybierz pozycję **Dodaj**.
5. Wybierz konto, do którego są stosowane uprawnienia (z konfiguracji Azure AD Connect).
6. Z listy rozwijanej **Zastosuj do** wybierz pozycję **obiekty podrzędne użytkownika**.
7. W obszarze **uprawnienia**zaznacz pola wyboru dla następujących opcji:
    * **Zmień hasło**
    * **Resetowanie hasła**
8. W obszarze **Właściwości**wybierz pola z następującymi opcjami:
    * **LockoutTime zapisu**
    * **PwdLastSet zapisu**
9. Wybierz pozycję **Zastosuj/OK** , aby zastosować zmiany i zamknąć wszystkie otwarte okna dialogowe.

Ze względu na to, że źródło urzędu jest lokalnie, zasady złożoności haseł są stosowane z tego samego połączonego źródła danych. Upewnij się, że zostały zmienione istniejące zasady grupy dla "minimalnego wieku hasła". Zasady grupy nie powinny mieć wartości 1, co oznacza, że hasło powinno mieć co najmniej dzień starego, zanim będzie można je zaktualizować. Musisz upewnić się, że jest ustawiona na 0. Te ustawienia można znaleźć w `gpmc.msc` w obszarze **Konfiguracja komputera > zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasad konta**. Uruchom `gpupdate /force`, aby upewnić się, że zmiana zacznie obowiązywać. 

## <a name="next-steps"></a>Następne kroki

[Co to jest zapisywanie zwrotne haseł?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włącz funkcję zapisywania zwrotnego haseł w Azure AD Connect"

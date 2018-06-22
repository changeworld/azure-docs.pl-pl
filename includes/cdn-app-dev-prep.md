## <a name="prerequisites"></a>Wymagania wstępne
Przed zapisaniem CDN kod zarządzania, należy wykonać pewne przygotowania do włączenia kodu do interakcji z Menedżerem zasobów Azure. Przygotowanie celu należy:

* Utwórz grupę zasobów zawiera profil CDN utworzonej w tym samouczku
* Konfigurowanie usługi Azure Active Directory do uwierzytelniania aplikacji
* Zastosuj uprawnienia do grupy zasobów, tak aby tylko autoryzowani użytkownicy z dzierżawy usługi Azure AD mogą współdziałać z profilu CDN

### <a name="creating-the-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**.
3. Wyszukaj **grupy zasobów** i w okienku grupy zasobów, kliknij przycisk **Utwórz**.

    ![Tworzenie nowej grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Określ nazwę grupy zasobów *CdnConsoleTutorial*.  Wybierz subskrypcję i wybierz lokalizację, w pobliżu.  Jeśli chcesz, możesz kliknąć **Przypnij do pulpitu nawigacyjnego** pole wyboru, aby przypiąć grupy zasobów do pulpitu nawigacyjnego w portalu.  Przypinanie ułatwia znajdowanie w przyszłości.  Po określeniu ustawień, kliknij przycisk **Utwórz**.

    ![Nazwy grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po utworzeniu grupy zasobów, jeśli nie został on Przypnij do pulpitu nawigacyjnego, możesz go znaleźć, klikając **Przeglądaj**, następnie **grup zasobów**.  Aby go otworzyć, kliknij grupę zasobów.  Zanotuj Twojej **identyfikator subskrypcji**. Potrzebujemy go później.

    ![Nazwy grupy zasobów](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Tworzenie aplikacji usługi Azure AD i stosowanie uprawnień
Istnieją dwa podejścia do uwierzytelniania aplikacji w usłudze Azure Active Directory: poszczególnych użytkowników lub nazwy głównej usługi. Nazwy głównej usługi jest podobny do konta usługi systemu Windows.  Zamiast udzielanie określonego użytkownika uprawnień do interakcji z profilów usługi CDN, zamiast tego przyznano uprawnienia do nazwy głównej usługi.  Nazwy główne usług są zwykle używane przez procesy zautomatyzowane, nieinterakcyjnym.  Mimo że w tym samouczku jest pisanie aplikacji konsoli interaktywne, firma Microsoft będzie skupić się na podejście głównej usługi.

Tworzenie nazwy głównej usługi składa się z kilku kroków, w tym tworzenie aplikacji usługi Azure Active Directory.  Aby go utworzyć, chcemy [czynności opisane w tym samouczku](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Należy postępować zgodnie z instrukcjami w [połączonego samouczek](../articles/resource-group-create-service-principal-portal.md).  Jest *ważne* zakończyć je dokładnie zgodnie z opisem.  Należy zwrócić uwagę użytkownika **Identyfikatorem dzierżawy**, **nazwę domeny dzierżawy** (często *. onmicrosoft.com* domeny przed określeniem domenę niestandardową), **identyfikator klienta** , i **klucza uwierzytelniania klienta**, potrzebujemy tych informacji później.  Należy zachować ostrożność w celu ochrony Twojego **identyfikator klienta** i **klucza uwierzytelniania klienta**, jako te poświadczenia mogą być używane przez każdy komputer można wykonać operacji jako podmiot zabezpieczeń usługi.
>
> Po przejściu do kroku o nazwie Konfiguracja wielodostępnych aplikacji, wybierz **nr**.
>
> Po przejściu do kroku [przypisywanie aplikacji do roli](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role), użyj grupy zasobów, utworzony wcześniej, *CdnConsoleTutorial*, ale zamiast **czytnika** roli, Przypisz  **Współautor profilu CDN** roli.  Po przypisaniu aplikacji **współautora profilu CDN** roli w danej grupie zasobów, wróć do tego samouczka. 
>
>

Po utworzeniu użytkownika nazwy głównej usługi i po przypisane **współautora profilu CDN** roli, **użytkowników** bloku grupy zasobów powinien wyglądać podobnie do poniższej ilustracji.

![Blok użytkowników](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interakcyjnego uwierzytelniania użytkownika
Jeśli zamiast nazwy głównej usługi, a nie trzeba uwierzytelnianie interakcyjne poszczególnych użytkowników, podobnie jak w przypadku nazwy głównej usługi jest proces.  W rzeczywistości należy postępuj zgodnie z tą samą procedurą, ale wprowadzić kilka drobne zmiany.

> [!IMPORTANT]
> Tylko wykonaj te czynności Jeśli wybierzesz uwierzytelniania indywidualnych użytkowników zamiast nazwy głównej usługi.
>
>

1. Podczas tworzenia aplikacji, zamiast **aplikacji sieci Web**, wybierz **aplikacji natywnej**.

    ![Aplikacja macierzysta](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na następnej stronie zostanie wyświetlony monit o **identyfikator URI przekierowania**.  Identyfikator URI nie można sprawdzić poprawności, ale pamiętaj został wprowadzony. Będzie potrzebny później.
3. Nie istnieje potrzeba do utworzenia **klucza uwierzytelniania klienta**.
4. Zamiast przypisywać nazwy głównej usługi do **współautora profilu CDN** roli, chcemy się przypisanie użytkownikom lub grupom.  W tym przykładzie widać, że zostały przypisane *użytkownika pokaz CDN* do **współautora profilu CDN** roli.  

    ![Dostęp użytkownika](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

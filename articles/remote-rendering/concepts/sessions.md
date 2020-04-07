---
title: Sesje zdalnego renderowania
description: Opisuje, czym jest sesja zdalnego renderowania
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681690"
---
# <a name="remote-rendering-sessions"></a>Sesje zdalnego renderowania

W usłudze Azure Remote Rendering (ARR) *sesja* jest kluczową koncepcją. W tym artykule wyjaśniono, czym dokładnie jest sesja.

## <a name="overview"></a>Omówienie

Renderowanie zdalne platformy Azure działa przez odciążanie złożonych zadań renderowania do chmury. Te zadania renderowania nie mogą być wykonywane przez dowolny serwer, ponieważ większość serwerów w chmurze nie ma procesorów gpu. Ze względu na ilość danych zaangażowanych i twarde wymaganie uzyskania wyników z interakcyjnym współczynnikiem klatek, odpowiedzialność, która serwer obsługuje, którego żądanie użytkownika również nie może być przekazana do innego komputera w locie, co może być możliwe w przypadku bardziej powszechnego ruchu internetowego.

Oznacza to, że w przypadku korzystania z usługi Azure Remote Rendering serwer w chmurze z niezbędnymi możliwościami sprzętowymi musi być zarezerwowany wyłącznie do obsługi żądań renderowania. *Sesja* odnosi się do wszystkiego, co związane z interakcji z tym serwerem. Zaczyna się od początkowego żądania zarezerwowania *(dzierżawy)* komputera do użytku, kontynuuje wszystkie polecenia ładowania i manipulowania modelami, a kończy się zwolnieniem dzierżawy na serwerze w chmurze.

## <a name="managing-sessions"></a>Zarządzanie sesjami

Istnieje wiele sposobów zarządzania sesjami i interakcji z nimi. Niezależny od języka sposób tworzenia, aktualizowania i zamykania sesji odbywa się za pośrednictwem [interfejsu API REST zarządzania sesjami.](../how-tos/session-rest-api.md) W językach C# i C++ te `AzureFrontend` `AzureSession`operacje są udostępniane za pośrednictwem klas i . W przypadku aplikacji Unity istnieją dalsze `ARRServiceUnity` funkcje narzędzia dostarczane przez składnik.

Po *nawiązaniu połączenia z* aktywną sesją operacje, takie jak `AzureSession` ładowanie [modeli](models.md) i interakcja ze sceną są udostępniane za pośrednictwem klasy.

### <a name="managing-multiple-sessions-simultaneously"></a>Jednoczesne zarządzanie wieloma sesjami

Nie jest możliwe pełne *połączenie z* wieloma sesjami z jednego urządzenia. Można jednak tworzyć, obserwować i zamykać dowolną liczbę sesji z jednej aplikacji. Tak długo, jak aplikacja nie jest przeznaczona do kiedykolwiek połączyć się z sesją, nie trzeba działać na urządzeniu takim jak HoloLens 2, albo. Przypadek użycia dla takiej implementacji może być, jeśli chcesz kontrolować sesje za pośrednictwem mechanizmu centralnego. Można na przykład utworzyć aplikację internetową, do której można się zalogować wiele tabletów i obiektywów HoloLenses. Następnie aplikacja może wyświetlać opcje na tabletach, takie jak model CAD do wyświetlenia. Jeśli użytkownik dokona wyboru, te informacje są przekazywane do wszystkich HoloLenses, aby utworzyć wspólne środowisko.

## <a name="session-phases"></a>Fazy sesji

Każda sesja przechodzi wiele faz.

### <a name="session-startup"></a>Uruchamianie sesji

Gdy poprosisz ARR o [utworzenie nowej sesji,](../how-tos/session-rest-api.md#create-a-session)pierwszą rzeczą, jaką robi, jest zwrócenie identyfikatora [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)sesji. Ten identyfikator UUID umożliwia wykonywanie zapytań o informacje dotyczące sesji. UUID i niektóre podstawowe informacje o sesji są zachowywane przez 30 dni, dzięki czemu można zbadać te informacje nawet po zakończeniu sesji. W tym momencie **stan sesji** zostanie zgłoszony jako **Starting**.

Następnie usługa Azure Remote Rendering próbuje znaleźć serwer, na który można obsługiwać sesję. Istnieją dwa parametry dla tego wyszukiwania. Po pierwsze, będzie rezerwować tylko serwery w Twoim [regionie](../reference/regions.md). To dlatego, że opóźnienie sieci w różnych regionach może być zbyt wysokie, aby zagwarantować przyzwoite doświadczenie. Drugi współczynnik to żądany *rozmiar* określony. W każdym regionie istnieje ograniczona liczba serwerów, które mogą spełnić żądanie rozmiaru *standardowego* lub *premium.* W związku z tym jeśli wszystkie serwery żądanego rozmiaru są obecnie używane w regionie, tworzenie sesji zakończy się niepowodzeniem. Przyczynę awarii [można zbadać](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Jeśli zażądasz *rozmiar standardowej* maszyny Wirtualnej, a żądanie nie powiedzie się z powodu wysokiego popytu, nie oznacza to, że żądanie serwera *Premium* również zakończy się niepowodzeniem. Więc jeśli jest to opcja dla Ciebie, możesz spróbować wrócić do maszyny Wirtualnej *Premium.*

Gdy usługa znajdzie odpowiedni serwer, musi skopiować na nią właściwą maszynę wirtualną (VM), aby przekształcić ją w hosta zdalnego renderowania platformy Azure. Ten proces trwa kilka minut. Następnie maszyna wirtualna jest uruchamiana, a **stan sesji** przechodzi do **gotowego**.

W tym momencie serwer oczekuje wyłącznie na dane wejściowe. Jest to również punkt, z którego otrzymasz naliczane rachunki za usługę.

### <a name="connecting-to-a-session"></a>Łączenie się z sesją

Gdy sesja będzie *gotowa,* można się z nią *połączyć.* Po podłączeniu urządzenie może wysyłać polecenia do ładowania i modyfikowania modeli. Każdy host ARR obsługuje tylko jedno urządzenie klienckie naraz, więc gdy klient łączy się z sesją, ma wyłączną kontrolę nad renderowanej zawartości. Oznacza to również, że wydajność renderowania nigdy nie będzie się różnić z przyczyn poza kontrolą.

> [!IMPORTANT]
> Chociaż tylko jeden klient może *połączyć się z* sesją, podstawowe informacje o sesjach, takie jak ich bieżący stan, mogą być wyszukiwane bez łączenia.

Gdy urządzenie jest połączone z sesją, próby połączenia przez inne urządzenia nie powiodą się. Jednak po rozłączeniu podłączonego urządzenia, dobrowolnie lub z powodu jakiegoś błędu, sesja zaakceptuje inne żądanie połączenia. Cały poprzedni stan (załadowane modele i takie) jest odrzucany w taki sposób, że następne urządzenie łączące otrzymuje czysty łupek. W ten sposób sesje mogą być ponownie wielokrotne, przez różne urządzenia i może być możliwe, aby ukryć obciążenie uruchamiania sesji od użytkownika końcowego w większości przypadków.

> [!IMPORTANT]
> Serwer zdalny nigdy nie zmienia stanu danych po stronie klienta. Wszystkie mutacje danych (takie jak aktualizacje transformacji i żądania obciążenia) muszą być wykonywane przez aplikację kliencką. Wszystkie akcje natychmiast zaktualizować stan klienta.

### <a name="session-end"></a>Koniec sesji

Podczas żądania nowej sesji należy określić *maksymalny czas dzierżawy*, zazwyczaj w zakresie od jednego do ośmiu godzin. Jest to czas trwania, podczas którego host zaakceptuje dane wejściowe.

Istnieją dwa regularne powody, dla sesji do końca. Ręcznie zażądasz wstrzymania sesji lub upływa maksymalny czas dzierżawy. W obu przypadkach każde aktywne połączenie z hostem jest natychmiast zamknięte, a usługa jest zamykana na tym serwerze. Serwer jest następnie przekazywał z powrotem do puli platformy Azure i może zostać zarekwirowany do innych celów. Nie można cofnąć ani anulować zatrzymania sesji. Kwerenda o **stan sesji** w zatrzymanej sesji zwróci **zatrzymaną** lub **wygasłą**, w zależności od tego, czy została ręcznie zamknięta, czy też osiągnięto maksymalny czas dzierżawy.

Sesja może również zostać zatrzymana z powodu awarii.

We wszystkich przypadkach nie będą naliczane kolejne rachunki po zatrzymaniu sesji.

> [!WARNING]
> To, czy łączysz się z sesją i jak długo, nie ma wpływu na rozliczenia. To, co płacisz za usługę, zależy od *czasu trwania sesji,* co oznacza czas, przez który serwer jest zarezerwowany wyłącznie dla Ciebie, oraz żądane możliwości sprzętowe (rozmiar maszyny Wirtualnej). Jeśli rozpoczniesz sesję, połącz się przez pięć minut, a następnie nie zatrzymaj sesji, tak aby działała do momentu wygaśnięcia dzierżawy, zostanie naliczona naliczona opłaty za pełny czas dzierżawy sesji. Z drugiej strony *maksymalny czas dzierżawy* jest głównie siatką bezpieczeństwa. Nie ma znaczenia, czy żądasz sesji z czasem dzierżawy ośmiu godzin, a następnie używasz jej tylko przez pięć minut, jeśli ręcznie zatrzymasz sesję później.

#### <a name="extend-a-sessions-lease-time"></a>Wydłużenie czasu dzierżawy sesji

Możesz [przedłużyć czas dzierżawy](../how-tos/session-rest-api.md#update-a-session) aktywnej sesji, jeśli okaże się, że jest potrzebna dłużej.

## <a name="example-code"></a>Przykładowy kod

Poniższy kod przedstawia prostą implementację uruchamiania sesji, oczekiwania na stan *gotowości,* łączenia, a następnie rozłączania i zamykania ponownie.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Wiele `AzureFrontend` `AzureSession` i wystąpień mogą być obsługiwane, manipulowane i wyszukiwane z kodu. Ale tylko jedno urządzenie może `AzureSession` łączyć się z jednocześnie.

Okres istnienia maszyny wirtualnej nie jest `AzureFrontend` powiązany `AzureSession` z wystąpieniem ani wystąpieniem. `AzureSession.StopAsync`należy wezwać do przerwania sesji.

Identyfikator sesji trwałej można wyszukiwać za pośrednictwem `AzureSession.SessionUUID()` i buforować lokalnie. Przy tym identyfikatorze aplikacja `AzureFrontend.OpenSession` może wywołać powiązanie z tą sesją.

Gdy `AzureSession.IsConnected` jest `AzureSession.Actions` true, zwraca `RemoteManager`wystąpienie , który zawiera funkcje [ładowania modeli,](models.md)manipulowania [elementów](entities.md)i [kwerendy informacji](../overview/features/spatial-queries.md) o renderowane sceny.

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Modele](models.md)

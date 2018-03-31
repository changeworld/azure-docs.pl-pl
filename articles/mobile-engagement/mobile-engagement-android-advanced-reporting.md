---
title: Zaawansowane opcje raportowania zestawem Azure Mobile Engagement Android SDK
description: Opis sposobu wykonywania zaawansowane raportowanie w celu przechwytywania analytics dla zestawem Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 419b65250d2b65e184e6e36349b17b5ac9e7a6ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Zaawansowane raporty dzięki zaangażowania w systemie Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

W tym temacie opisano dodatkowe scenariusze raportowania w aplikacji systemu Android. Te opcje można zastosować do aplikacji utworzonych w [wprowadzenie](mobile-engagement-android-get-started.md) samouczka.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Samouczek, które zostały ukończone został celowo bezpośredniego i prostego, ale są zaawansowane opcje, które można wybrać.

## <a name="modifying-your-activity-classes"></a>Modyfikowanie użytkownika `Activity` klas
W [Wprowadzenie — samouczek](mobile-engagement-android-get-started.md), wszystkie musiał wykonać było Twojej `*Activity` podklasy dziedziczyć odpowiadającego `Engagement*Activity` klasy. Na przykład jeśli Twoje działanie starszej wersji rozszerzony `ListActivity`, możesz spowodowałoby, że rozszerzenie `EngagementListActivity`.

> [!IMPORTANT]
> Korzystając z `EngagementListActivity` lub `EngagementExpandableListActivity`, upewnij się, że wszelkie wywołanie `requestWindowFeature(...);` staje się przed wywołaniem do `super.onCreate(...);`, w przeciwnym razie wystąpi awaria.
> 
> 

Te klasy w można znaleźć `src` folderu i skopiować je do projektu. Klasy są również w **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternatywna metoda: wywołanie `startActivity()` i `endActivity()` ręcznie
Jeśli nie możesz lub nie było przeciążyć Twojej `Activity` klas, można zamiast tego rozpoczęcia i zakończenia działań przez wywołanie metody `EngagementAgent`przez metody bezpośrednio.

> [!IMPORTANT]
> Zestaw SDK systemu Android nigdy nie wywołuje `endActivity()` metody, nawet wtedy, gdy aplikacja zostanie zamknięta (w systemie Android, aplikacje są nigdy nie zamknięte). W związku z tym jest *wysokiej* zaleca, aby wywołać `startActivity()` metody w `onResume` wywołania zwrotnego z *wszystkie* działaniami i `endActivity()` metody w `onPause()` wywołania zwrotnego z *wszystkie* działań. To jest jedynym sposobem należy upewnić się, że nie przedostają sesji. Jeśli przeciek sesji usługi Engagement nigdy nie zakończy połączenie z zapleczem usługi Engagement (ponieważ usługa połączono tak długo, jak długo trwa oczekiwanie na sesji).
> 
> 

Oto przykład:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

W tym przykładzie jest podobny do `EngagementActivity` klasy i jej wariantów, którego kod źródłowy jest udostępniany w `src` folderu.

## <a name="using-applicationoncreate"></a>Przy użyciu Application.onCreate()
Kod umieszczony w `Application.onCreate()` i w innej aplikacji wywołania zwrotne jest wykonywane dla procesów wszystkich aplikacji, w tym usługi Engagement. Może mieć niepożądane efekty uboczne, takich jak przydziału pamięci niepotrzebnych i wątków w procesie stopnia zaangażowania, lub zduplikowane odbiorniki emisji lub usługi.

Jeśli można zastąpić `Application.onCreate()`, zaleca się dodawania poniższy fragment kodu na początku Twojej `Application.onCreate()` funkcji:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Tak samo postąpić w `Application.onTerminate()`, `Application.onLowMemory()`, i `Application.onConfigurationChanged(...)`.

Można rozszerzać `EngagementApplication` zamiast rozszerzanie `Application`: wywołanie zwrotne `Application.onCreate()` jest wyboru procesu i wywołania `Application.onApplicationProcessCreate()` tylko jeśli bieżący proces nie jest obsługującym usługę zaangażowania, te same zasady poprosić o innych wywołań zwrotnych.

## <a name="tags-in-the-androidmanifestxml-file"></a>Tagi w pliku AndroidManifest.xml
W tagu usługi w pliku AndroidManifest.xml `android:label` atrybutu umożliwia wybór nazwy usługi Engagement wyświetlaną użytkownikom końcowym na ekranie telefon "Uruchamianie usługi". Firma Microsoft zaleca ustawienie tego atrybutu na `"<Your application name>Service"` (na przykład `"AcmeFunGameService"`).

Określanie `android:process` atrybutu zapewnia, że usługi Engagement działa we własnym procesie (uruchamianie zaangażowania w ramach tego samego procesu, zgodnie z aplikacji sprawia, że Twoje main/wątku interfejsu użytkownika jest potencjalnie mniej dynamiczne).

## <a name="building-with-proguard"></a>Kompilowanie z użyciem ProGuard
W przypadku tworzenia pakietu aplikacji z narzędzia ProGuard, należy zachować niektóre klasy. Możesz użyć następującego fragmentu kodu konfiguracji:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }

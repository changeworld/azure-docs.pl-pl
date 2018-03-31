---
title: Wycofaj Unity samouczek piłka
description: Kroki, aby utworzyć klasycznego Unity Przywróć gra w piłkę, czyli jako warunek wstępny dla wszystkich samouczków Unity usługi Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 52d5962645b1408fdba61ec1bf4e4f682b80905b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a id="unity-roll-a-ball"></a>Utwórz Przywróć Unity grę piłka
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten samouczek przedstawia kroki głównego nieco zmodyfikowany [Unity Przywróć samouczek piłka](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Gry próbki składa się z obiektu kulistego player, który jest kontrolowany przez użytkownika aplikacji i celem gry jest "zbieranie" kolekcjonowanych obiektów przez kolizji obiektu player z tymi obiektami kolekcjonowanych. Przy założeniu, podstawowa znajomość środowiska Edytor platformy Unity. Jeśli wystąpiły problemy powinien zapoznaj się z samouczkiem pełna. 

### <a name="setting-up-the-game"></a>Konfigurowanie gry
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Otwórz **Edytor platformy Unity** i kliknij przycisk **nowy**. 
   
    ![][51] 
2. Podaj **Nazwa projektu** & **lokalizacji**, wybierz pozycję **3D** i kliknij przycisk **Tworzenie projektu**.
   
    ![][52]
3. Zapisz sceny domyślne właśnie utworzony jako część nowego projektu jako o nazwie **MiniGame** w ramach nowej  **\_sceny** folder **zasoby** folderu:
   
    ![][53]
4. Utwórz **obiektu 3D -> płaszczyzny** jako odtwarzanie i zmienić nazwę tego obiektu płaszczyzny jako **podstaw**
   
    ![][1]
5. Resetuj składnika transformacji dla tego **podstaw** obiekt tak, aby w źródle. 
   
    ![][3]
6. Usuń zaznaczenie pola wyboru **Pokaż siatkę** z **Gizmos menu** dla **podstaw** obiektu.
   
    ![][4]
7. Aktualizacja **skali** składnik **podstaw** być obiekt [X = 2, T = 1, Z = 2]. 
   
    ![][5]
8. Dodaj nową **obiektu 3D -> kuli** do projektu i Zmień nazwę tego kuli obiekt jako **Player**. 
   
    ![][6]
9. Wybierz **Player** obiekt i kliknij przycisk **zresetować przekształcenie** podobny do obiektu płaszczyzny. 
10. Aktualizacja **Transform -> pozycji -> współrzędną Y** składnika y Player jako 0,5.  
    
    ![][7]
11. Utwórz nowy folder o nazwie **materiałów** w projekcie, gdzie utworzymy materiały do odtwarzacza kolorów. 
12. Utwórz nową **materiału** o nazwie **tła** w tym folderze. 
    
    ![][8]
13. Zaktualizuj kolor materiałów, aktualizując **Albedo** jego właściwości. Można wybrać wartości RGB [0,32,64]. 
    
    ![][9]
14. Przeciągnij w tym materiale do widoku sceny umożliwia stosowanie koloru do **podstaw** obiektu. 
    
    ![][10]
15. Na koniec zaktualizuj **Transform -> Obrót -> Y** do 60 obiektu światła dla uzyskania przejrzystości. 
    
    ![][12]

### <a name="moving-the-player"></a>Przenoszenie Windows Media player
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Dodaj **RigidBody** składnika do **Player** obiektu. 
   
    ![][13]
2. Utwórz nowy folder o nazwie **skryptów** w projekcie. 
3. Kliknij przycisk **Dodaj składnik -> Nowy skrypt -> C# skrypt**. Nadaj mu nazwę **PlayerController**i kliknij przycisk **Utwórz i Dodaj**. Spowoduje to utworzenie i Dołącz skrypt do obiektu Player.  
   
    ![][14]
4. Przenieś ten skrypt, w obszarze **skryptów** folderu w projekcie. 
5. Otwórz skrypt do edycji w Edytorze skryptów Ulubione, zaktualizuj kod skryptu z następującym kodem i zapisz go. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Należy pamiętać, że skrypt powyżej używa **szybkości** właściwości. W edytorze Unity zaktualizuj właściwość szybkości do 10.  
   
    ![][15]
7. Trafienia **odtwarzanie** Edytor platformy Unity. Teraz powinno być możliwe do kontrolowania piłka przy użyciu klawiatury i powinna obracania i przenoszenia. 

### <a name="moving-the-camera"></a>Przenoszenie aparatu
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) i będzie powiązać **głównego aparatu** do **Player** obiektu. 

1. Aktualizacja **Transform.Position** jako X = 0, Y = 10.5, Z =-10.  
2. Aktualizacja **Transform.Rotation** jako X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Dodaj nowy skrypt o nazwie **CameraController** do **MainCamera** i umieści ją w folderze skryptów. 
   
    ![][17]
4. Otwarcie skryptu do edycji i Dodaj następujący kod w tym:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. W środowisku Unity — przeciągnij zmiennej Player w gnieździe odtwarzacza dla obiektu głównego aparatu tak, aby dwa są skojarzone ze sobą. 
   
    ![][18]
6. Jeśli naciśnij pozycję Odtwórz w edytorze Unity i obrót obiektu Player piłka następnie zostanie wyświetlone aparatu w ruchu.  

### <a name="setting-up-the-play-area"></a>Konfigurowanie obszaru odtwarzania
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Utworzymy ściany otaczającego podstaw, dzięki czemu obiektu Player piłka nie pozostawienia obszaru odtwarzania w ruchu. 

1. Kliknij przycisk **Utwórz -> Utwórz pusty -> obiekt gry** i nadaj mu nazwę **ściany**
   
    ![][19]
2. W tym obiektu ściany — Utwórz nową **obiektu 3D -> modułu** i nadaj jej nazwę na "Zachodnie tablicy". 
   
    ![][20]
3. Aktualizacji **Transform -> pozycji** i **Transform -> skali** dla tego obiektu zachodnie tablicy. 
   
    ![][21]
4. Zduplikowana wall Zachodnia, aby utworzyć **wall wschód** z zaktualizowanego przekształcenie położenie i Skala. 
   
    ![][22]
5. Zduplikowana tablicy Wschodnia, aby utworzyć **wall Północna** z zaktualizowanego przekształcenie pozycji & skali. 
   
    ![][23]
6. Zduplikowana wall Północna i Utwórz **wall Południowa** z zaktualizowanego przekształcenie pozycji & skali. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Tworzenie obiektów kolekcjonowanych
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Utworzymy niektórych atrakcyjne obiektów wyglądającej stanowiące zestawu kolekcjonowanego obiektów, które obiekt piłka Player musi zebrać przez kolizji z nich. 

1. Utwórz nową **3D obiektu modułu** i nadaj mu nazwę pobrania. 
2. Dostosuj **Transform -> Obrót** & **Transform -> skali** podnoszenia obiektu. 
   
    ![][25]
3. Utwórz i Dołącz **nowego skryptu C#** o nazwie **Rotator** do pobrania obiektu. Upewnij się umieścić skrypt w folderze skryptów. 
   
    ![][26]
4. Otwórz ten skrypt do edycji i zaktualizować go na następujący: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Teraz trafiony tryb gry w edytorze Unity i pokazu podnoszenia obiektu można obracanie na osi.
6. Utwórz nowy folder o nazwie **Prefabs** 
   
    ![][27]
7. Przeciągnij **podnoszenia** obiektu i umieść ją w folderze Prefabs.
   
    ![][28]
8. Utwórz nową **gry pusty obiekt** o nazwie **Pickups**. Resetowanie położenia do źródła, a następnie przeciągnij podnoszenia obiektu pod ten obiekt gier.  
   
    ![][29]
9. Zduplikowana **podnoszenia** obiekt i utworzyć na **podstaw** obiekt wokół **Player** obiektu aktualizując **X & ZTransform.Positionw** odpowiednio wartości. 
   
    ![][30]
10. Utwórz **nowe materiały** o nazwie **podnoszenia** i zaktualizować je będzie kolor czerwony, aktualizując **właściwości Albedo** podobnie jak robiliśmy dla obiekt podstaw aktualizacji. 
    
    ![][31]
11. Zastosuj materiałów do 4 podnoszenia obiektów.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Zbieranie obiektów podnoszenia
Poniższe kroki są z [samouczek Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Odtwarzacz będą aktualizowane tak, że jest w stanie "collect" podnoszenia obiektów przez kolizji z nich. 

1. Otwórz **PlayerController** skryptu dołączony do obiektu Player do edycji i zaktualizować ją do następującego:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Utwórz nową **Tag** o nazwie **wybierz się** (musi być zgodna nowości w skrypcie)  
   
    ![][33]
   
    ![][34]
3. Zastosuj tę **Tag** do pobrania Prefab obiektu. 
   
    ![][35]
4. Włącz **IsTrigger** wyboru dla obiekt Prefab.
   
    ![][36]
5. Dodaj treść sztywne do pobrania Prefab obiektu. Aby zoptymalizować wydajność modyfikacjom collider statycznych, które zostały użyte do dynamicznego collider. 
   
    ![][37]
6. Na koniec sprawdź **IsKinematic** właściwość prefab obiektu. 
   
    ![][38]
7. Trafienia **odtwarzanie** w jednolitości edytora i będzie można odtworzyć ten **Przywróć piłka** gier za pomocą przenoszenia obiektu Player przy użyciu kluczy klawiatury dla kierunku danych wejściowych. 

### <a name="updating-the-game-for-mobile-play"></a>Aktualizowanie gry dla przenośnych play
Powyżej zawartych podstawowy samouczek z Unity. Firma Microsoft będzie teraz zmodyfikować grę, aby była urządzenia przenośnego przyjazną. Należy zwrócić uwagę na to, My używamy danych wprowadzonych z klawiatury w grze wykonanej do tej pory do testowania. Obecnie firma Microsoft będzie zmodyfikuj go, aby odtwarzacz można sterować przy użyciu ruchu telefonu, np. przy użyciu przyspieszeniomierza jako dane wejściowe. 

Otwórz **PlayerController** skryptów do edycji i aktualizacji **FixedUpdate** metodę ruchu z przyspieszeniomierza przeniesienie obiektu Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Ten samouczek zawiera podstawowe tworzenia gier z Unity i wdrażać ją na urządzeniu wybranym grę. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png














Maszyny wirtualne na platformie Azure można utworzyć za pomocą Eksploratora serwera w programie Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Utwórz maszynę wirtualną platformy Azure w Eksploratorze serwera
Można tworzyć maszynę wirtualną w [portalu zarządzania Azure](http://go.microsoft.com/fwlink/?LinkID=253103), można również tworzyć maszyny wirtualne na platformie Azure, za pomocą poleceń w Eksploratorze serwera. Maszyny wirtualne można, na przykład, aby zapewnić fronton za wspólnej równoważeniem obciążenia publiczny punkt końcowy.

### <a name="to-create-a-new-virtual-machine"></a>Aby utworzyć nową maszynę wirtualną
1. Otwórz w Eksploratorze serwera **Azure** węzeł i kliknij przycisk **maszyn wirtualnych**.
2. W menu kontekstowym kliknij **Utwórz maszynę wirtualną**.
   
    **Tworzenia nowej maszyny wirtualnej** zostanie wyświetlony Kreator.
   
    ![Polecenie Utwórz maszynę wirtualną](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. Na **Wybierz subskrypcję** strony, wybierz subskrypcję do użycia podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **dalej**.
   
    Jeśli użytkownik nie jest obecnie zalogowany na platformie Azure, kliknij przycisk **logowania** do logowania. Następnie wybierz subskrypcji platformy Azure w polu listy rozwijanej, jeśli nie jest zaznaczony.
4. Na **Wybieranie obrazu maszyny wirtualnej** wybierz typ obrazu w **typ obrazu** listy rozwijanej pola listy, a następnie wybierz obrazy maszyny wirtualnej w **nazwa obrazu** pola listy . Gdy wszystko będzie gotowe, kliknij przycisk **dalej**.
   
    ![Wybierz stronę obrazu maszyny wirtualnej](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Można wybrać następujące typy obrazów.
   
   * **Obrazy publicznego** listy obrazów maszyny wirtualnej, systemy operacyjne i oprogramowanie serwera, takie jak Windows Server i SQL Server.
   * **Obrazów MSDN** listy obrazów maszyny wirtualnej dostępne dla subskrybentów MSDN, takie jak Visual Studio i Microsoft Dynamics oprogramowania.
   * **Obrazy prywatnego** listy specjalizowany i uogólniony obrazy maszyny wirtualnej, które zostały utworzone.
     
     Informacje na temat specjalistyczne i ogólnych maszyn wirtualnych, zobacz [obrazu maszyny Wirtualnej](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Zobacz [jak przechwycić maszynę wirtualną systemu Windows do użycia jako szablon](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) dla informacji o sposobie wyłączyć maszynę wirtualną do szablonu, który można szybko utworzyć nowy wstępnie skonfigurowanych maszyn wirtualnych.
     
     Można kliknąć nazwę obrazu maszyny wirtualnej, aby zobaczyć informacje o obrazie w prawej części strony.
     
     > [!NOTE]
     > Nie można dodać obrazy maszyny wirtualnej do **publicznego obrazy** lub **obrazów MSDN** listy, ponieważ są one tylko do odczytu. Wszystkie maszyny wirtualne tworzone są dodawane do **obrazów prywatnego** listy.
     > 
     > 
     
     Jeśli jesteś subskrybentem MSDN z subskrypcją programu Visual Studio na poziomie, można utworzyć wbudowanych Azure maszyny wirtualnej, która zawiera programu Visual Studio, jak również inne obrazy. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę wirtualną w programie Visual Studio za pomocą obrazów programu Visual Studio 2013 galerii obrazu dla subskrybentów MSDN](http://visualstudio2013msdngalleryimage.azurewebsites.net) i [subskrypcji MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs). |
5. Na **ustawienia podstawowej maszyny wirtualnej** strony, wprowadź nazwę komputera, a następnie dodać specyfikacje dla maszyny wirtualnej, w tym rozmiar, a nazwę użytkownika i hasło. Gdy wszystko będzie gotowe, kliknij przycisk **dalej**.
   
    Aby zalogować się do komputera przy użyciu pulpitu zdalnego, dlatego jest dobrym rozwiązaniem jest zapisanie ich w dół w przypadku zapomnienia użyjesz nową nazwę i hasło. Po utworzeniu maszyny wirtualnej platformy Azure w programie Visual Studio, można zmienić jej rozmiaru i inne ustawienia w [portalu zarządzania Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Jeśli wybierzesz dla maszyny wirtualnej o większych rozmiarach, mogą stosować dodatkowych opłat. Zobacz [maszyny wirtualne — cennik](https://azure.microsoft.com/pricing/details/virtual-machines/) Aby uzyskać więcej informacji.
   > 
   > 
6. Maszyny wirtualne utworzone w programie Visual Studio wymaga usługi w chmurze. Na **ustawienia usługi w chmurze** wybierz usługę chmury dla maszyny wirtualnej lub kliknij przycisk **< Utwórz nowy... >** na liście rozwijanej, jeśli nie masz jeszcze chmury usługi lub chcesz użyć nowego. Konto magazynu jest również wymagany, dlatego wybierz konto magazynu (lub Utwórz nowe konto magazynu) w **konta magazynu** pole listy rozwijanej. Zobacz [wprowadzenie do usługi Microsoft Azure Storage](../articles/storage/common/storage-introduction.md) Aby uzyskać więcej informacji.
7. Jeśli chcesz określić sieć wirtualną (która jest opcjonalny), zaznacz go w sieci wirtualnej i podsieci pola listy rozwijanej.
   
    Maszyny wirtualne, które są członkami zestawu dostępności są wdrażane dla domen różnych błędów. Zobacz [sieci wirtualnej Azure](https://azure.microsoft.com/services/virtual-network/) Aby uzyskać więcej informacji.
8. Jeśli chcesz maszyny wirtualnej należy do zestawu dostępności (również opcjonalnie) zaznacz **określ zestaw dostępności** zaznacz pole wyboru, a następnie wybierz pozycję zestawem dostępności w polu listy rozwijanej. Gdy wszystko będzie gotowe, wybierz pozycję **dalej** przycisku.
   
    Dodawanie maszyny wirtualnej do zestawu dostępności pomaga aplikacji pozostają dostępne podczas awarie sieci, awarie sprzętowe dysku lokalnym oraz wszelkich planowanych przestojów. Należy użyć [portalu zarządzania Azure](http://go.microsoft.com/fwlink/?LinkID=253103) do utworzenia dostępności, podsieci i sieci wirtualne zestawy. Zobacz [Zarządzanie dostępność maszyn wirtualnych](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) Aby uzyskać więcej informacji.
9. Na **punkty końcowe** Określ publiczne punkty końcowe, które mają być dostępne dla użytkowników na komputerze wirtualnym. Na przykład można włączyć protokołu HTTP (Port 80) oprócz pulpitu zdalnego i punkty końcowe programu PowerShell, które są domyślnie włączone. Aby dodać punkt końcowy, wybierz kolejno **nazwa portu** pole listy rozwijanej, a następnie wybierz pozycję **Dodaj** przycisku. Aby usunąć punkt końcowy, wybierz kolorem czerwonym **X** obok nazwy na liście punktów końcowych.
   
    ![Strona punkty końcowe w Kreatorze maszyn wirtualnych.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Punkty końcowe, które są dostępne są zależne od wybranej dla maszyny wirtualnej usługi w chmurze. Zobacz [punktami końcowymi usług Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) Aby uzyskać więcej informacji.
   
   > [!NOTE]
   > Włączanie publiczne punkty końcowe udostępnia usługi na maszynie wirtualnej z Internetem. Upewnij się zainstalować i skonfigurować prawidłowo punktów końcowych i usług na maszynie wirtualnej, takich jak kontrola dostępu ustawienie listy (kontroli dostępu ACL) dla punktów końcowych. Zobacz [jak się punkty końcowe z maszyną wirtualną](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) Aby uzyskać więcej informacji.
   > 
   > 
10. Po zakończeniu konfigurowania ustawień maszyny wirtualnej, wybierz **Utwórz** przycisk, aby utworzyć maszynę wirtualną.
    
     Jako platforma Azure tworzy maszynę wirtualną **dziennika aktywności platformy Azure** będzie wyświetlany postęp operacji tworzenia maszyny wirtualnej.
    
     ![Dziennik aktywności maszyny wirtualnej — w toku.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Aby wyświetlić tylko informacje o maszynie wirtualnej, wybierz **maszyn wirtualnych** karcie **dziennika aktywności platformy Azure**.
    
     ![Dziennik aktywności maszyny wirtualnej — ukończone.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Jeśli operacja zakończy się pomyślnie, nowej maszyny wirtualnej jest wyświetlany w obszarze **maszyn wirtualnych** węzła w Eksploratorze serwera. Możesz zalogować się do go, klikając **łączyć się przy użyciu pulpitu zdalnego** skrótów.
    
     ![Maszyny wirtualnej znajdujących się w Eksploratorze serwera.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Zarządzanie maszyny wirtualne
Na stronie konfiguracji maszyny wirtualnej, oprócz zamykanie, połączenie odświeżania i dodawanie punktów kontrolnych do wybraną maszynę wirtualną możesz wyświetlić lub zmienić ustawienia maszyny wirtualnej. Możesz:

* Zmień rozmiar maszyny wirtualnej.
* Wybierz zestaw do użycia z maszyną wirtualną dostępności.
* Dodać, usunąć lub zmienić ustawienia dla publicznych punktów końcowych.
* Dodać, usunąć lub skonfigurować rozszerzenia maszyny wirtualnej.
* Wyświetl informacje o dyskach skojarzoną z maszyną wirtualną.

### <a name="view-or-change-virtual-machine-settings"></a>Wyświetl lub zmień ustawienia maszyny wirtualnej
1. W Eksploratorze serwera, wybierz maszynę wirtualną w **maszyny wirtualne Azure** węzła.
2. W menu skrótów wybierz **Konfiguruj** do wyświetlania na stronie konfiguracji maszyny wirtualnej.
   
    ![Na stronie konfiguracji maszyny wirtualnej platformy Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Wyświetlanie informacji o maszynie wirtualnej lub go zmienić.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Zapisz lub Przywróć stan maszyny wirtualnej
Skonfiguruj maszynę wirtualną i instalować oprogramowanie na nim, jest dobrym pomysłem jest regularnie zapisać postęp przez utworzenie punktów kontrolnych maszyn wirtualnych. Punkt kontrolny to migawka lub obraz, bieżący stan maszyny wirtualnej. Jeśli wystąpią problemy z maszyną wirtualną lub chcesz zmienić konfigurację maszyny wirtualnej, można oszczędzić czas, przez przywrócenie poprzedniego stanu punktu kontrolnego, zamiast Rozpoczynanie od zera.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Aby utworzyć punkt kontrolny maszyny wirtualnej
1. W Eksploratorze serwera, wybierz maszynę wirtualną w **maszyny wirtualne Azure** węzła.
2. W menu skrótów wybierz **Konfiguruj** do wyświetlania na stronie konfiguracji maszyny wirtualnej.
3. Na stronie konfiguracji wybierz **Przechwyć obraz** przycisku.
   
    ![Przycisku przechwytywania strony konfiguracji platformy Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    **Przechwytywanie maszyny wirtualnej** zostanie wyświetlone okno dialogowe.
   
    ![Okno dialogowe maszyny wirtualnej Azure przechwytywania](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Podaj etykietę obrazu i opis. Podano etykiety domyślnej i opis, ale można je zastąpić, jeśli chcesz własnymi.
5. Jeśli program Sysprep zostało już uruchomione na tej maszynie wirtualnej, wybierz **program Sysprep został uruchomiony na maszynie wirtualnej** pole.
   
    Sysprep to narzędzie, które między innymi usuwa dane specyficzne dla systemów z maszyny wirtualnej wersji systemu Windows, dzięki czemu szablonu używanego przez innych użytkowników. Zobacz [jak przechwycić maszynę wirtualną systemu Windows do użycia jako szablon](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) Aby uzyskać więcej informacji. Utwórz kopię zapasową maszyny Wirtualnej przed uruchomieniem programu Sysprep.
6. Po zakończeniu konfigurowania ustawień przechwytywania, wybierz **przechwytywania** przycisk, aby utworzyć punkt kontrolny.
   
    Jako platforma Azure tworzy punkt kontrolny, **dziennika aktywności platformy Azure** będzie wyświetlany postęp operacji.
   
    ![Przechwytywanie punktu kontrolnego maszyny wirtualnej](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Po zakończeniu operacji punktu kontrolnego, zobaczysz go w **dziennika aktywności platformy Azure**.
   
    ![Zakończono operację punktu kontrolnego](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Aby zarządzać punktami kontrolnymi maszyny wirtualnej
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Aby przywrócić wcześniej zapisany stan maszyny wirtualnej
* Wykonaj kroki opisane w temacie [krok po kroku: wykonaj chmury przywraca Microsoft maszyn wirtualnych Azure przy użyciu programu PowerShell — część 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Aby usunąć punkt kontrolny
1. Przejdź do [portalu zarządzania Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
2. Na stronie konfiguracji maszyny wirtualnej wybierz **obrazów** kartę w górnej części strony.
3. Wybierz punkt kontrolny, aby usunąć, a następnie wybierz pozycję **usunąć** u dołu strony.

## <a name="shut-down-your-virtual-machine"></a>Zamknij maszynę wirtualną
1. W Eksploratorze serwera, wybierz maszynę wirtualną, aby wyłączyć w **maszyny wirtualne Azure** węzła.
2. W menu skrótów wybierz **zamknięcia** polecenie lub wybierz **Konfiguruj** wyświetlić na stronie konfiguracji maszyny wirtualnej, a następnie wybierz pozycję **zamknięcia** przycisku.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tworzenia maszyn wirtualnych, zobacz [tworzenia maszyny wirtualnej systemem Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [Utwórz maszynę wirtualną z systemem Windows w portalu Azure w wersji zapoznawczej](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


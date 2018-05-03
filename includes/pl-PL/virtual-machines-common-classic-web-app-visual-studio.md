

Po utworzeniu projektu aplikacji sieci web dla platformy Azure można udostępnić maszynę wirtualną na platformie Azure. Następnie można skonfigurować maszynę wirtualną z dodatkowe oprogramowanie lub użyć maszyny wirtualnej do celów diagnostycznych lub debugowania.

Aby utworzyć maszynę wirtualną podczas tworzenia aplikacji sieci web, wykonaj następujące kroki:

1. W programie Visual Studio, kliknij przycisk **pliku** > **nowy** > **projektu** > **Web**, a następnie wybierz pozycję **Aplikacji sieci Web ASP.NET** (w obszarze **Visual C#** lub **Visual Basic** węzłach).
2. W **nowy projekt ASP.NET** okno dialogowe, wybierz typ aplikacji sieci web ma i upewnij się, że w sekcji Azure okna dialogowego (w prawym dolnym rogu) **Hostuj w chmurze** jest pole wyboru wybrane (to pole wyboru jest oznaczony **Utwórz zasoby zdalne** w niektórych instalacji).
   
    ![][0]
3. Na przykład w obszarze Microsoft Azure, na liście rozwijanej wybierz **maszyny wirtualnej (wersja 1)**, a następnie kliknij przycisk **OK** przycisku.
4. Zaloguj się do platformy Azure, jeśli zostanie wyświetlony monit. **Utwórz maszynę wirtualną** zostanie wyświetlone okno dialogowe.
   
    ![][2]
5. W **nazwy DNS** wprowadź nazwę maszyny wirtualnej. Nazwa DNS musi być unikatowa na platformie Azure. Jeśli wprowadzona nazwa jest niedostępna, zostanie wyświetlony czerwony wykrzyknik.
6. W **obrazu** wybierz obraz, którego chcesz utworzyć maszynę wirtualną na. Można wybrać obrazy standardowe maszyny wirtualnej platformy Azure lub obrazu przekazywanego do platformy Azure.
7. Pozostaw **Włącz usługi IIS i Web Deploy** pole wyboru jest zaznaczone, jeśli nie chcesz zainstalować serwer innej witryny sieci web. Nie można opublikować w programie Visual Studio po wyłączeniu narzędzia Web Deploy. Usługi IIS i Web Deploy można dodać do żadnych pakietów obrazów systemu Windows Server, w tym obrazów niestandardowych.
8. W **rozmiar** wybierz rozmiar maszyny wirtualnej.
9. Określ poświadczenia logowania dla tej maszyny wirtualnej. Zwróć uwagę, ponieważ konieczne będzie ich dostęp do komputera za pośrednictwem pulpitu zdalnego.
10. W **lokalizacji** wybierz region do hosta maszyny wirtualnej.
11. Kliknij przycisk **OK** przycisk, aby rozpocząć tworzenie maszyny wirtualnej. Możesz śledzić postęp operacji w **dane wyjściowe** okna.
    
    ![][3]
12. Po zainicjowaniu obsługi maszyny wirtualnej, opublikowanych skrypty są tworzone w **PublishScripts** węzła w rozwiązaniu. Opublikowane skrypt jest uruchamiany i przepisy maszynę wirtualną na platformie Azure. **Dane wyjściowe** okno wyświetla stan. Skrypt wykonuje następujące czynności, aby skonfigurować maszynę wirtualną:
    
    * Tworzy maszynę wirtualną, jeśli jeszcze nie istnieje.
    * Tworzy konto magazynu, którego nazwa zaczyna się od `devtest`, ale tylko wtedy, gdy nie ma już konto magazynu w wybranym regionie.
    * Tworzy usługi w chmurze jako kontener dla maszyny wirtualnej i tworzy rolę sieci web dla aplikacji sieci web.
    * Konfiguruje narzędzie Web Deploy na maszynie wirtualnej.
    * Umożliwia skonfigurowanie usług IIS i platformy ASP.NET na maszynie wirtualnej.
    
    ![][4]
13. (Opcjonalnie) Można połączyć do nowej maszyny wirtualnej. W **Eksploratora serwera**, rozwiń węzeł **maszyn wirtualnych** węzła, wybieranie węzła dla tworzenia maszyny wirtualnej, a jego menu skrótów, **Połącz przy użyciu pulpitu zdalnego**. Alternatywnie w **Eksplorator chmury** można **Otwórz w portalu** menu skrótów i nawiąż połączenie z maszyną wirtualną.
    
    ![][5]

## <a name="next-steps"></a>Następne kroki
Jeśli chcesz dostosować opublikowanych skryptów tworzenia, odczytu bardziej szczegółowe informacje o [za pomocą skryptów programu PowerShell systemu Windows do opublikowania deweloperów i środowisk testowych](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png

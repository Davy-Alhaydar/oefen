# oefen


// src/Controller/HomeController.php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class HomeController extends AbstractController
{
    #[Route('/', name: 'app_home')]
    public function index(): Response
    {
        // Haal de ingelogde gebruiker op
        $currentUser = $this->getUser();

        if ($currentUser) {
            // De gebruiker is ingelogd
            $username = $currentUser->getUserIdentifier(); // of getUsername(), afhankelijk van je User entity
            // Bijvoorbeeld: dump($username); of geef het door aan Twig
        } else {
            // De gebruiker is NIET ingelogd
            // Misschien wil je hier redirecten of een melding geven
        }

        return $this->render('home/index.html.twig', [
            'user' => $currentUser
        ]);
    }
}




{% if user %}
    Welkom, {{ user.userIdentifier }}
{% else %}
    Je bent niet ingelogd.
{% endif %}
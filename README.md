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













// src/Controller/ProductController.php

use App\Entity\Product;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class ProductController extends AbstractController
{
    #[Route('/product/{id}', name: 'product_show')]
    public function show(int $id, EntityManagerInterface $em): Response
    {
        // Haal het product op uit de database
        $product = $em->getRepository(Product::class)->find($id);

        if (!$product) {
            throw $this->createNotFoundException('Product niet gevonden');
        }

        // Geef het door aan Twig
        return $this->render('product/show.html.twig', [
            'product' => $product
        ]);
    }
}












{# templates/product/show.html.twig #}

<h1>{{ product.name }}</h1>
<p>Prijs: €{{ product.prijs }}</p>
<p>Beschrijving: {{ product.omschrijving }}</p>

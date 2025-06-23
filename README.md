# oefen


// src/Controller/ProductController.php

namespace App\Controller;

use App\Repository\ProductRepository;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class ProductController extends AbstractController
{
    #[Route('/producten', name: 'product_index')]
    public function index(ProductRepository $productRepository): Response
    {
        // Haal alle producten op
        $producten = $productRepository->findAll();

        // Stuur naar Twig
        return $this->render('product/index.html.twig', [
            'producten' => $producten,
        ]);
    }
}







<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <title>Producten</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 2rem;
            background-color: #f4f4f4;
        }
        h1 {
            color: #333;
        }
        .product {
            background-color: white;
            padding: 1rem;
            margin-bottom: 1rem;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .prijs {
            color: green;
            font-weight: bold;
        }
    </style>
</head>
<body>

<h1>Alle Producten</h1>

{% if producten is empty %}
    <p>Er zijn nog geen producten beschikbaar.</p>
{% else %}
    {% for product in producten %}
        <div class="product">
            <h2>{{ product.description }}</h2>
            <p class="prijs">Prijs: €{{ product.prijs }}</p>
        </div>
    {% endfor %}
{% endif %}

</body>
</html>










goede

<?php

namespace App\Controller;

use App\Repository\ProductRepository;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class ProductController extends AbstractController
{
    #[Route('/producten', name: 'product_index')]
    public function index(ProductRepository $productRepository): Response
    {
        // Haal alle producten op uit de database
        $producten = $productRepository->findAll();

        // Geef de producten door aan de Twig-template
        return $this->render('product/index.html.twig', [
            'producten' => $producten,
        ]);
    }
}


            




# oefen


!!!!! SchoolGroupController.php !!!!!

<?php

namespace App\Controller;

use App\Entity\SchoolGroup;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class SchoolGroupController extends AbstractController
{
    #[Route('/', name: 'app_school_group')]
    public function index(): Response
    {
        return $this->render('school_group/index.html.twig');
    }

    #[Route('/school_group', name: 'app_school_group_show')]
    public function show(EntityManagerInterface $entityManager): Response
    {
        $schoolGroups = $entityManager->getRepository(SchoolGroup::class)->findAll();

        return $this->render('school_group/show.html.twig', [
            'school_groups' => $schoolGroups
        ]);
    }

    #[Route('/school_group/{id}', name: 'app_school_group_detail')]
    public function detail(SchoolGroup $schoolGroup): Response
    {
        return $this->render('school_group/students.html.twig', [
            'schoolGroup' => $schoolGroup,
            'students' => $schoolGroup->getStudents(),
        ]);
    }

}




!!!!! StudentController.php !!!!!!

<?php

namespace App\Controller;

use App\Entity\Student;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;

class StudentController extends AbstractController
{
    #[Route('/student', name: 'app_student')]
    public function index(EntityManagerInterface $entityManager): Response
    {
        $students = $entityManager->getRepository(Student::class)->findAll();
        return $this->render('student/index.html.twig', [
            'students' => $students,
        ]);
    }

    #[Route('/student/{id}', name: 'app_show_student')]
    public function showStudent(EntityManagerInterface $entityManager, int $id): Response
    {
        $student = $entityManager->getRepository(Student::class)->find($id);
        return $this->render('student/show-student.html.twig', [
            'student' => $student,
        ]);
    }
}


!!!!! school_group/index.html.twig !!!!!!!

{% extends 'base.html.twig' %}

{% block body %}
<!-- Masthead Avatar Image-->
<img class="masthead-avatar mb-5" src="{{ asset('/images/avataaars.svg') }}" alt="Heppie Kamper" />
<!-- Masthead Heading-->
<h1 class="masthead-heading text-uppercase mb-0">
    School de Heppie Kemper
</h1>
<!-- Icon Divider-->
<div class="divider-custom divider-light">
    <div class="divider-custom-line"></div>
    <div class="divider-custom-icon"><i class="fas fa-star"></i></div>
    <div class="divider-custom-line"></div>
</div>
<!-- Masthead Subheading-->
<p class="masthead-subheading font-weight-light mb-0">
    Software Developer - IT Systems and Devices - Medewerker ICT Support
</p>
{% endblock %}







!!!!!! school_group/show.html.twig !!!!!!!

{% extends 'base.html.twig' %}

{% block body %}
<h1 class="masthead-heading text-uppercase mb-0">
    Klassen overzicht
</h1>
<table class="table table-hover table-striped">
    <thead>
    <tr>
        <th>Naam</th>
        <th>Jaar</th>
        <th>SLB'er</th>
        <th>Klas tonen</th>

    </tr>
    </thead>
    <tbody>
        {% for group in school_groups %}
            <tr>

<td>
{{ group.name }}
</td>

                <td>{{ group.year }}</td>
                <td>{{ group.teacher }}</td>
                <td>
                    <a href="{{ path('app_school_group_detail', { id: group.id }) }}" class="btn text-primary-emphasis">
                        Toon studenten
                    </a>
                </td>
            </tr>
        {% endfor %}
    </tbody>
</table>
{% endblock %}





!!!!!! school_group/students.html.twig   zelf aanmaken !!!!!!!

{% extends 'base.html.twig' %}

{% block body %}
    <h1>Studenten in klas {{ schoolGroup.name }}</h1>

    <table class="table table-striped">
        <thead>
        <tr>
            <th>Voornaam</th>
            <th>Achternaam</th>
            <th>Email</th>
        </tr>
        </thead>
        <tbody>
        {% for student in students %}
            <tr>
                <td>{{ student.firstName }}</td>
                <td>{{ student.lastName }}</td>
                <td>{{ student.email }}</td>
            </tr>
        {% else %}
            <tr>
                <td colspan="3">Geen studenten in deze klas.</td>
            </tr>
        {% endfor %}
        </tbody>
    </table>

    <a href="{{ path('app_school_group_show') }}">← Terug naar klassenoverzicht</a>
{% endblock %}



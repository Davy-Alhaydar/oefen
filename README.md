# oefen
https://github.com/NorbuBustinduyMarin/dd

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








!!!!! StudentController.php !!!!!!

Edit(bewerken)

#[Route('/student/{id}/edit', name: 'app_student_edit')]
public function edit(Request $request, EntityManagerInterface $em, Student $student): Response
{
    // Form aanmaken
    $form = $this->createForm(StudentType::class, $student);

    // Request data verwerken
    $form->handleRequest($request);

    if ($form->isSubmitted() && $form->isValid()) {
        $em->flush();

        // Na opslaan, terug naar overzicht
        return $this->redirectToRoute('app_student_index');
    }

    return $this->render('student/edit.html.twig', [
        'form' => $form->createView(),
        'student' => $student,
    ]);
}


delete

#[Route('/student/{id}/delete', name: 'app_student_delete', methods: ['POST'])]
public function delete(Request $request, EntityManagerInterface $em, Student $student): Response
{
    if ($this->isCsrfTokenValid('delete'.$student->getId(), $request->request->get('_token'))) {
        $em->remove($student);
        $em->flush();
    }

    return $this->redirectToRoute('app_student_index');
}


studentType form maken

php bin/console make:form StudentType


!!!!! templates/student/edit.html.twig !!!!!!!

{% extends 'base.html.twig' %}

{% block body %}
    <h1>Student bewerken</h1>

    {{ form_start(form) }}
        {{ form_widget(form) }}
        <button class="btn btn-primary">Opslaan</button>
    {{ form_end(form) }}

    <a href="{{ path('app_student_index') }}">Terug naar overzicht</a>
{% endblock %}



!!!!!! templates/student/index.html.twig !!!!!!!

<td>
    <a class="btn btn-outline-secondary" href="{{ path('app_student_edit', {'id': student.id}) }}">Bewerk</a>

    <form method="post" action="{{ path('app_student_delete', {'id': student.id}) }}" style="display:inline-block" onsubmit="return confirm('Weet je het zeker?');">
        <input type="hidden" name="_token" value="{{ csrf_token('delete' ~ student.id) }}">
        <button class="btn btn-outline-danger">Verwijder</button>
    </form>
</td>

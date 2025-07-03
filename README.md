# oefen


!!!!!! index.php !!!!!!

<?php
// Verbinding maken met database
$conn = new mysqli("localhost", "root", "", "cars4u");

// Check verbinding
if ($conn->connect_error) {
    die("Verbinding mislukt: " . $conn->connect_error);
}

// Haal gegevens op
$sql = "SELECT * FROM cars";
$result = $conn->query($sql);
?>

<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <title>Auto Overzicht</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
    <h1 class="mb-4">Auto Overzicht</h1>

    <a href="insert.php" class="btn btn-primary mb-3">Nieuwe auto toevoegen</a>

    <table class="table table-bordered table-striped">
        <thead class="table-dark">
        <tr>
            <th>Merk</th>
            <th>Type</th>
            <th>Kilometerstand</th>
            <th>Kleur</th>
            <th>Prijs</th>
            <th>Acties</th>
        </tr>
        </thead>
        <tbody>
        <?php if ($result && $result->num_rows > 0): ?>
            <?php while($row = $result->fetch_assoc()): ?>
                <tr>
                    <td><?= htmlspecialchars($row["merk"]) ?></td>
                    <td><?= htmlspecialchars($row["type"]) ?></td>
                    <td><?= htmlspecialchars($row["kilometerstand"]) ?></td>
                    <td><?= htmlspecialchars($row["kleur"]) ?></td>
                    <td>€ <?= htmlspecialchars(number_format($row["prijs"], 2, ',', '.')) ?></td>
                    <td>
                        <a href="update.php?id=<?= $row['id'] ?>" class="btn btn-warning btn-sm">Update</a>
                        <a href="delete.php?id=<?= $row['id'] ?>" class="btn btn-danger btn-sm">Delete</a>
                    </td>
                </tr>
            <?php endwhile; ?>
        <?php else: ?>
            <tr><td colspan="6" class="text-center">Geen auto's gevonden.</td></tr>
        <?php endif; ?>
        </tbody>
    </table>
</div>
</body>
</html>







!!!!! insert.php !!!!!

<?php
$errors = [];
$values = [
    'merk' => '',
    'type' => '',
    'kilometerstand' => '',
    'kleur' => '',
    'prijs' => ''
];

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    foreach ($values as $key => $val) {
        $values[$key] = trim($_POST[$key] ?? '');
    }

    // Validatie
    if ($values['merk'] == '') {
        $errors['merk'] = "Merk is verplicht.";
    }

    if ($values['type'] == '') {
        $errors['type'] = "Type is verplicht.";
    }

    if (!ctype_digit($values['kilometerstand'])) {
        $errors['kilometerstand'] = "Kilometerstand moet een geheel getal zijn.";
    }

    if ($values['kleur'] == '') {
        $errors['kleur'] = "Kleur is verplicht.";
    }

    if (!is_numeric($values['prijs'])) {
        $errors['prijs'] = "Prijs moet een getal zijn.";
    }

    // Als geen fouten → opslaan
    if (empty($errors)) {
        $conn = new mysqli("localhost", "root", "", "cars4u");

        if ($conn->connect_error) {
            die("Verbinding mislukt: " . $conn->connect_error);
        }

        $stmt = $conn->prepare("INSERT INTO cars (merk, type, kilometerstand, kleur, prijs) VALUES (?, ?, ?, ?, ?)");
        $stmt->bind_param("ssisd", $values['merk'], $values['type'], $values['kilometerstand'], $values['kleur'], $values['prijs']);
        $stmt->execute();
        $stmt->close();
        $conn->close();

        header("Location: index.php");
        exit;
    }
}
?>

<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <title>Nieuwe Auto Toevoegen</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
    <h1 class="mb-4">Nieuwe Auto Toevoegen</h1>
    <form method="post">
        <?php
        function field($name, $label, $type = 'text') {
            global $values, $errors;
            $error = $errors[$name] ?? '';
            $value = htmlspecialchars($values[$name]);
            $class = $error ? 'is-invalid' : '';
            echo "
                <div class='mb-3'>
                    <label for='$name' class='form-label'>$label</label>
                    <input type='$type' class='form-control $class' id='$name' name='$name' value='$value'>
                    <div class='invalid-feedback'>$error</div>
                </div>
            ";
        }

        field('merk', 'Merk');
        field('type', 'Type');
        field('kilometerstand', 'Kilometerstand');
        field('kleur', 'Kleur');
        field('prijs', 'Prijs (bijv. 12345.67)');
        ?>
        <button type="submit" class="btn btn-success">Toevoegen</button>
        <a href="index.php" class="btn btn-secondary">Terug</a>
    </form>
</div>
</body>
</html>







!!!!! update.php !!!!!!

<?php
$conn = new mysqli("localhost", "root", "", "cars4u");
if ($conn->connect_error) {
    die("Verbinding mislukt: " . $conn->connect_error);
}

$errors = [];
$values = [
    'merk' => '',
    'type' => '',
    'kilometerstand' => '',
    'kleur' => '',
    'prijs' => ''
];

// ID ophalen
$id = isset($_GET['id']) ? intval($_GET['id']) : 0;
if ($id <= 0) {
    die("Ongeldige ID.");
}

// Als formulier is verstuurd (POST)
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    foreach ($values as $key => $val) {
        $values[$key] = trim($_POST[$key] ?? '');
    }

    // Validatie
    if ($values['merk'] == '') $errors['merk'] = "Merk is verplicht.";
    if ($values['type'] == '') $errors['type'] = "Type is verplicht.";
    if (!ctype_digit($values['kilometerstand'])) $errors['kilometerstand'] = "Moet een geheel getal zijn.";
    if ($values['kleur'] == '') $errors['kleur'] = "Kleur is verplicht.";
    if (!is_numeric($values['prijs'])) $errors['prijs'] = "Prijs moet een getal zijn.";

    // Als geen fouten → updaten
    if (empty($errors)) {
        $stmt = $conn->prepare("UPDATE cars SET merk=?, type=?, kilometerstand=?, kleur=?, prijs=? WHERE id=?");
        $stmt->bind_param("ssisdi", $values['merk'], $values['type'], $values['kilometerstand'], $values['kleur'], $values['prijs'], $id);
        $stmt->execute();
        $stmt->close();
        $conn->close();

        header("Location: index.php");
        exit;
    }
} else {
    // Ophalen bestaande gegevens (GET)
    $stmt = $conn->prepare("SELECT * FROM cars WHERE id = ?");
    $stmt->bind_param("i", $id);
    $stmt->execute();
    $result = $stmt->get_result();
    if ($row = $result->fetch_assoc()) {
        foreach ($values as $key => $_) {
            $values[$key] = $row[$key];
        }
    } else {
        die("Auto niet gevonden.");
    }
    $stmt->close();
}
$conn->close();
?>

<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <title>Auto Aanpassen</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
    <h1 class="mb-4">Auto Aanpassen</h1>
    <form method="post">
        <?php
        function field($name, $label, $type = 'text') {
            global $values, $errors;
            $error = $errors[$name] ?? '';
            $value = htmlspecialchars($values[$name]);
            $class = $error ? 'is-invalid' : '';
            echo "
                <div class='mb-3'>
                    <label for='$name' class='form-label'>$label</label>
                    <input type='$type' class='form-control $class' id='$name' name='$name' value='$value'>
                    <div class='invalid-feedback'>$error</div>
                </div>
            ";
        }

        field('merk', 'Merk');
        field('type', 'Type');
        field('kilometerstand', 'Kilometerstand');
        field('kleur', 'Kleur');
        field('prijs', 'Prijs (bijv. 12345.67)');
        ?>
        <button type="submit" class="btn btn-primary">Opslaan</button>
        <a href="index.php" class="btn btn-secondary">Annuleren</a>
    </form>
</div>
</body>
</html>





!!!!! delete.php !!!!!!

<?php
$conn = new mysqli("localhost", "root", "", "cars4u");
if ($conn->connect_error) {
    die("Verbinding mislukt: " . $conn->connect_error);
}

$id = isset($_GET['id']) ? intval($_GET['id']) : 0;

if ($id <= 0) {
    die("Ongeldige ID.");
}

// Verwijderen bij bevestiging
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    if (isset($_POST['bevestig'])) {
        $stmt = $conn->prepare("DELETE FROM cars WHERE id = ?");
        $stmt->bind_param("i", $id);
        $stmt->execute();
        $stmt->close();
    }
    $conn->close();
    header("Location: index.php");
    exit;
}

// Gegevens ophalen voor bevestiging
$stmt = $conn->prepare("SELECT * FROM cars WHERE id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
$result = $stmt->get_result();
$car = $result->fetch_assoc();
$stmt->close();

if (!$car) {
    die("Auto niet gevonden.");
}
$conn->close();
?>

<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <title>Auto Verwijderen</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
    <h1 class="mb-4 text-danger">Auto Verwijderen</h1>
    <p>Weet je zeker dat je deze auto wilt verwijderen?</p>
    <ul>
        <li><strong>Merk:</strong> <?= htmlspecialchars($car['merk']) ?></li>
        <li><strong>Type:</strong> <?= htmlspecialchars($car['type']) ?></li>
        <li><strong>Kilometerstand:</strong> <?= htmlspecialchars($car['kilometerstand']) ?></li>
        <li><strong>Kleur:</strong> <?= htmlspecialchars($car['kleur']) ?></li>
        <li><strong>Prijs:</strong> € <?= htmlspecialchars(number_format($car['prijs'], 2, ',', '.')) ?></li>
    </ul>

    <form method="post" class="d-flex gap-2">
        <button type="submit" name="bevestig" class="btn btn-danger">Ja, verwijderen</button>
        <a href="index.php" class="btn btn-secondary">Annuleren</a>
    </form>
</div>
</body>
</html>

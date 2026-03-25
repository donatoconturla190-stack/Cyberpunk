# Cyberpunk
Cuberbots
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>CyberBots - Prototipos de Robótica</title>
    <style>
        /* Reset básico */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Orbitron', sans-serif;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            color: #00ffea;
            min-height: 100vh;
        }

        header {
            background-color: rgba(0,0,0,0.7);
            padding: 20px;
            text-align: center;
            font-size: 2em;
            border-bottom: 2px solid #00ffea;
            letter-spacing: 2px;
        }

        main {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            padding: 20px;
            gap: 20px;
        }

        .robot-card {
            background-color: rgba(10,10,10,0.8);
            border: 2px solid #ff00ff;
            border-radius: 15px;
            padding: 15px;
            width: 250px;
            display: flex;
            flex-direction: column;
            align-items: center;
            transition: transform 0.3s;
        }

        .robot-card:hover {
            transform: scale(1.05);
        }

        .robot-card img {
            width: 100%;
            border-radius: 10px;
            margin-bottom: 10px;
            border: 1px solid #00ffea;
        }

        .robot-card h3 {
            margin-bottom: 5px;
            color: #ff00ff;
        }

        .robot-card p {
            margin-bottom: 10px;
            font-size: 0.9em;
        }

        .robot-card .price {
            font-weight: bold;
            margin-bottom: 10px;
            color: #00ffea;
        }

        .robot-card button {
            background-color: #ff00ff;
            color: #000;
            border: none;
            padding: 8px 15px;
            cursor: pointer;
            font-weight: bold;
            border-radius: 5px;
            transition: background-color 0.3s;
        }

        .robot-card button:hover {
            background-color: #00ffea;
            color: #000;
        }

        /* Carrito lateral */
        #cart {
            position: fixed;
            top: 20px;
            right: 20px;
            background-color: rgba(0,0,0,0.9);
            padding: 15px;
            border: 2px solid #ff00ff;
            border-radius: 10px;
            max-width: 250px;
        }

        #cart h2 {
            margin-bottom: 10px;
            color: #ff00ff;
            font-size: 1.2em;
        }

        #cart ul {
            list-style: none;
            max-height: 200px;
            overflow-y: auto;
            margin-bottom: 10px;
        }

        #cart ul li {
            margin-bottom: 5px;
            font-size: 0.9em;
        }

        #cart button {
            background-color: #00ffea;
            color: #000;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
            font-weight: bold;
            border-radius: 5px;
        }

        /* Panel de edición (solo admin) */
        #admin-panel {
            position: fixed;
            bottom: 20px;
            left: 20px;
            background-color: rgba(0,0,0,0.9);
            padding: 15px;
            border: 2px solid #00ffea;
            border-radius: 10px;
            max-width: 300px;
        }

        #admin-panel h2 {
            color: #00ffea;
            font-size: 1.2em;
            margin-bottom: 10px;
        }

        #admin-panel input, #admin-panel textarea {
            width: 100%;
            margin-bottom: 5px;
            padding: 5px;
            border-radius: 5px;
            border: 1px solid #00ffea;
            background-color: rgba(0,0,0,0.7);
            color: #00ffea;
        }

        #admin-panel button {
            background-color: #ff00ff;
            color: #000;
            border: none;
            padding: 8px;
            cursor: pointer;
            font-weight: bold;
            border-radius: 5px;
            width: 100%;
        }

        #admin-panel button:hover {
            background-color: #00ffea;
            color: #000;
        }
    </style>
</head>
<body>

<header>CyberBots - Robótica hecha a mano</header>

<main id="product-container">
    <!-- Robots se agregan dinámicamente -->
</main>

<div id="cart">
    <h2>Carrito</h2>
    <ul id="cart-items"></ul>
    <strong>Total: $<span id="cart-total">0</span></strong>
    <br>
    <button onclick="checkout()">Comprar</button>
</div>

<div id="admin-panel">
    <h2>Panel Admin</h2>
    <input type="text" id="robot-name" placeholder="Nombre del robot">
    <textarea id="robot-description" placeholder="Descripción"></textarea>
    <input type="number" id="robot-price" placeholder="Precio">
    <input type="text" id="robot-image" placeholder="URL de imagen">
    <button onclick="addRobot()">Agregar Robot</button>
</div>

<script>
    const products = [];
    const cart = [];

    const productContainer = document.getElementById('product-container');
    const cartItems = document.getElementById('cart-items');
    const cartTotal = document.getElementById('cart-total');

    function renderProducts() {
        productContainer.innerHTML = '';
        products.forEach((robot, index) => {
            const card = document.createElement('div');
            card.className = 'robot-card';
            card.innerHTML = `
                <img src="${robot.image}" alt="${robot.name}">
                <h3>${robot.name}</h3>
                <p>${robot.description}</p>
                <div class="price">$${robot.price}</div>
                <button onclick="addToCart(${index})">Agregar al carrito</button>
            `;
            productContainer.appendChild(card);
        });
    }

    function addRobot() {
        const name = document.getElementById('robot-name').value;
        const description = document.getElementById('robot-description').value;
        const price = parseFloat(document.getElementById('robot-price').value);
        const image = document.getElementById('robot-image').value;

        if(name && description && price && image) {
            products.push({name, description, price, image});
            renderProducts();
            // Limpiar inputs
            document.getElementById('robot-name').value = '';
            document.getElementById('robot-description').value = '';
            document.getElementById('robot-price').value = '';
            document.getElementById('robot-image').value = '';
        } else {
            alert('Completa todos los campos.');
        }
    }

    function addToCart(index) {
        cart.push(products[index]);
        renderCart();
    }

    function renderCart() {
        cartItems.innerHTML = '';
        let total = 0;
        cart.forEach(item => {
            const li = document.createElement('li');
            li.textContent = `${item.name} - $${item.price}`;
            cartItems.appendChild(li);
            total += item.price;
        });
        cartTotal.textContent = total;
    }

    function checkout() {
        if(cart.length === 0){
            alert("El carrito está vacío");
        } else {
            alert("Gracias por tu compra, total: $" + cartTotal.textContent);
            cart.length = 0;
            renderCart();
        }
    }
</script>

</body>
</html>

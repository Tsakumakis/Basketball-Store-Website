<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Coolio Sports</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Arial', sans-serif; line-height: 1.6; color: #333; }
        header { background: linear-gradient(135deg, #e85d04 0%, #dc2f02 100%); color: white; padding: 1rem 0; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        nav { max-width: 1200px; margin: 0 auto; display: flex; justify-content: space-between; align-items: center; padding: 0 2rem; }
        .logo { font-size: 1.8rem; font-weight: bold; }
        .nav-links { display: flex; gap: 2rem; list-style: none; }
        .nav-links a { color: white; text-decoration: none; transition: opacity 0.3s; }
        .cart-icon { background: rgba(255,255,255,0.2); padding: 0.5rem 1rem; border-radius: 5px; cursor: pointer; }
        .hero { background: #370617; color: white; text-align: center; padding: 6rem 2rem; }
        .hero h1 { font-size: 3rem; margin-bottom: 1rem; }
        .cta-button { background: #e85d04; color: white; padding: 1rem 2rem; text-decoration: none; border-radius: 5px; display: inline-block; }
        .products { max-width: 1200px; margin: 3rem auto; padding: 0 2rem; }
        .section-title { text-align: center; font-size: 2.5rem; margin-bottom: 3rem; color: #370617; }
        .product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 2rem; }
        .product-card { border: 1px solid #ddd; border-radius: 10px; overflow: hidden; background: white; transition: transform 0.3s; box-shadow: 0 2px 8px rgba(0,0,0,0.07); }
        .product-card:hover { transform: translateY(-5px); }
        .product-image { width: 100%; height: 200px; background: #eee; display: flex; align-items: center; justify-content: center; font-size: 4rem; }
        .product-info { padding: 1.5rem; }
        .product-name { font-size: 1.2rem; font-weight: bold; margin-bottom: 0.5rem; }
        .product-price { font-size: 1.5rem; color: #e85d04; font-weight: bold; }
        .product-stock { margin: 0.4rem 0 0.8rem; color: #555; font-size: 0.95rem; }
        .status-badge { position: fixed; top: 20px; right: 20px; background: white; padding: 1rem; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.2); z-index: 1000; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; }
        .modal-content { background: white; margin: 5% auto; padding: 2rem; max-width: 600px; border-radius: 10px; }
        .modal-content h2 { margin-bottom: 1rem; }
        .cart-item { display: flex; justify-content: space-between; padding: 0.5rem 0; border-bottom: 1px solid #eee; }
        .cart-total { font-size: 1.2rem; font-weight: bold; margin-top: 1rem; }
        .modal-close { margin-top: 1rem; background: #370617; color: white; border: none; padding: 0.6rem 1.5rem; border-radius: 5px; cursor: pointer; }
        .add-to-cart { background: #370617; color: white; border: none; padding: 0.75rem; width: 100%; border-radius: 5px; cursor: pointer; margin-top: 10px; font-size: 1rem; }
        .add-to-cart:hover { background: #e85d04; transition: background 0.2s; }
        .empty-cart { color: #888; padding: 1rem 0; }
    </style>
</head>
<body>
    <header>
        <nav>
            <div class="logo">Coolio Sports</div>
            <ul class="nav-links">
                <li><a href="#home">Home</a></li>
                <li><a href="#products">Products</a></li>
            </ul>
            <div class="cart-icon" onclick="openCartModal()">🛒 Cart (<span id="cartCount">0</span>)</div>
        </nav>
    </header>

    <section class="hero" id="home">
        <h1>Welcome to Coolio</h1>
        <p>Your #1 destination for sports gear</p>
        <br><br>
        <a href="#products" class="cta-button">Shop Now</a>
    </section>

    <section class="products" id="products">
        <h2 class="section-title">Our Products</h2>
        <div class="product-grid" id="productGrid"></div>
    </section>

    <div id="cartModal" class="modal">
        <div class="modal-content">
            <h2>🛒 Your Cart</h2>
            <div id="cartItems"></div>
            <p class="cart-total">Total: $<span id="cartTotalAmount">0.00</span></p>
            <button class="modal-close" onclick="closeCartModal()">Close</button>
        </div>
    </div>

    <script>
        const products = [
            { id: 1, name: "Basketball",         price: 49.99,  stock: 24, image: "https://github.com/Tsakumakis/Basketball-Store-Website/blob/main/OIP-2687664637.jpg?raw=true" },
            { id: 2, name: "Athletic Shorts",        price: 29.99,  stock: 50, image: "https://github.com/Tsakumakis/Basketball-Store-Website/blob/main/OIP-639133713.jpg?raw=true" },
            { id: 3, name: "Sports Water Bottle",    price: 19.99,  stock: 60, image: "https://github.com/Tsakumakis/Basketball-Store-Website/blob/main/OIP-2791108295.jpg?raw=true" },
            { id: 4, name: "Knee Brace Support",     price: 24.99,  stock: 30, image: "https://github.com/Tsakumakis/Basketball-Store-Website/blob/main/OIP-1154291207.jpg?raw=true" },
        ];

        const cart = {};

        function showNotification(message, isSuccess = true) {
            const badge = document.createElement('div');
            badge.className = 'status-badge';
            badge.innerHTML = `${isSuccess ? '✅' : '❌'} ${message}`;
            document.body.appendChild(badge);
            setTimeout(() => badge.remove(), 3000);
        }

        function renderProducts() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = products.map(p => `
                <div class="product-card">
                    <div class="product-image"><img src="${p.image}" alt="${p.name}" style="width:100%;height:100%;object-fit:contain;"></div>
                    <div class="product-info">
                        <div class="product-name">${p.name}</div>
                        <div class="product-price">$${p.price.toFixed(2)}</div>
                        <p class="product-stock">Stock: ${p.stock}</p>
                        <button class="add-to-cart" onclick="addToCart(${p.id})">Add to Cart</button>
                    </div>
                </div>
            `).join('');
        }

        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            if (!product) return;
            cart[productId] = (cart[productId] || 0) + 1;
            updateCartCount();
            showNotification(`Added ${product.name} to cart!`);
        }

        function updateCartCount() {
            const count = Object.values(cart).reduce((sum, qty) => sum + qty, 0);
            document.getElementById('cartCount').textContent = count;
        }

        function openCartModal() {
            const cartItemsEl = document.getElementById('cartItems');
            const entries = Object.entries(cart);

            if (entries.length === 0) {
                cartItemsEl.innerHTML = '<p class="empty-cart">Your cart is empty.</p>';
                document.getElementById('cartTotalAmount').textContent = '0.00';
            } else {
                let total = 0;
                cartItemsEl.innerHTML = entries.map(([id, qty]) => {
                    const p = products.find(p => p.id === parseInt(id));
                    const subtotal = p.price * qty;
                    total += subtotal;
                    return `<div class="cart-item"><span>${p.emoji} ${p.name} × ${qty}</span><span>$${subtotal.toFixed(2)}</span></div>`;
                }).join('');
                document.getElementById('cartTotalAmount').textContent = total.toFixed(2);
            }

            document.getElementById('cartModal').style.display = 'block';
        }

        function closeCartModal() {
            document.getElementById('cartModal').style.display = 'none';
        }

        // Close modal on backdrop click
        document.getElementById('cartModal').addEventListener('click', function(e) {
            if (e.target === this) closeCartModal();
        });

        renderProducts();
    </script>
</body>
</html>

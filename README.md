<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DropifyJS - Advanced Dropshipping with Cart and Coupons</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f3f4f6;
      color: #333;
      padding: 20px;
    }
    h1, h2 {
      color: #2b6cb0;
    }
    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 30px;
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
    }
    .header {
      text-align: center;
      margin-bottom: 40px;
    }
    .product-list, .cart-list, .order-summary {
      margin-bottom: 40px;
    }
    .product, .cart-item, .order-item {
      background: #edf2f7;
      padding: 20px;
      margin: 10px 0;
      border-radius: 8px;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }
    .product-name, .cart-name, .order-name {
      font-weight: bold;
      font-size: 1.2rem;
    }
    .product-info, .cart-info, .order-info {
      margin-top: 10px;
      font-size: 1rem;
    }
    .btn {
      padding: 12px 24px;
      font-size: 16px;
      color: white;
      background-color: #4CAF50;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.3s;
    }
    .btn:hover {
      background-color: #45a049;
    }
    .order-status {
      font-weight: bold;
    }
    .status-pending { color: #f6ad55; }
    .status-shipped { color: #38a169; }
    .status-cancelled { color: #e53e3e; }
    .order-summary {
      background: #e2e8f0;
      padding: 20px;
      border-radius: 8px;
      margin-top: 20px;
    }
    .coupon-field {
      margin-top: 20px;
    }
    .coupon-input {
      padding: 10px;
      border: 2px solid #ddd;
      border-radius: 5px;
    }
  </style>
</head>
<body>

  <div class="container">
    <div class="header">
      <h1>Welcome to DropifyJS - Advanced Dropshipping Service</h1>
      <p>Simulate shopping, cart management, and order placement with discounts.</p>
    </div>
    
    <div class="product-list">
      <h2>📦 Available Products</h2>
      <div id="products"></div>
    </div>
    
    <div class="cart-list">
      <h2>🛒 Your Shopping Cart</h2>
      <div id="cart"></div>
      <button class="btn" onclick="checkout()">Checkout</button>
    </div>
    
    <div class="order-summary">
      <h2>Order Summary</h2>
      <div id="order-summary"></div>
      <div class="coupon-field">
        <input class="coupon-input" type="text" id="coupon-code" placeholder="Enter coupon code">
        <button class="btn" onclick="applyCoupon()">Apply Coupon</button>
      </div>
    </div>
    
    <div style="text-align: center; margin-top: 40px;">
      <button class="btn" onclick="simulateOrder()">Simulate Order</button>
    </div>
  </div>

  <script>
    class Product {
      constructor(id, name, priceUSD) {
        this.id = id;
        this.name = name;
        this.priceUSD = priceUSD;
      }

      info() {
        return `<div class="product">
                  <div class="product-name">${this.name}</div>
                  <div class="product-info">Price: $${this.priceUSD}</div>
                  <button class="btn" onclick="addToCart(${this.id})">Add to Cart</button>
                </div>`;
      }
    }

    class CartItem {
      constructor(product, quantity) {
        this.product = product;
        this.quantity = quantity;
      }

      totalPrice() {
        return this.product.priceUSD * this.quantity;
      }

      info() {
        return `<div class="cart-item">
                  <div class="cart-name">${this.product.name}</div>
                  <div class="cart-info">Quantity: ${this.quantity} | Total: $${this.totalPrice()}</div>
                </div>`;
      }
    }

    class DropShipService {
      constructor() {
        this.products = [];
        this.cart = [];
        this.orders = [];
        this.coupons = [
          { code: 'DISCOUNT10', discount: 0.1 },
          { code: 'FREESHIP', discount: 0.2 }
        ];
        this.initSampleProducts();
      }

      initSampleProducts() {
        this.products.push(new Product(101, "Wireless Earbuds", 39.99));
        this.products.push(new Product(102, "LED Gaming Mouse", 24.99));
        this.products.push(new Product(103, "USB-C Charger", 19.99));
        this.products.push(new Product(104, "Smartwatch", 89.99));
      }

      renderProducts() {
        let productHTML = '';
        this.products.forEach(p => productHTML += p.info());
        document.getElementById('products').innerHTML = productHTML;
      }

      addToCart(productId) {
        const product = this.products.find(p => p.id === productId);
        const existingItem = this.cart.find(item => item.product.id === productId);

        if (existingItem) {
          existingItem.quantity++;
        } else {
          const newItem = new CartItem(product, 1);
          this.cart.push(newItem);
        }

        this.renderCart();
      }

      renderCart() {
        let cartHTML = '';
        this.cart.forEach(item => cartHTML += item.info());
        document.getElementById('cart').innerHTML = cartHTML;
        this.updateOrderSummary();
      }

      applyCoupon() {
        const couponCode = document.getElementById('coupon-code').value;
        const coupon = this.coupons.find(c => c.code === couponCode);
        if (coupon) {
          this.discount = coupon.discount;
          alert(`Coupon applied! You get a ${coupon.discount * 100}% discount.`);
        } else {
          alert("Invalid coupon code!");
        }
        this.updateOrderSummary();
      }

      updateOrderSummary() {
        let total = this.cart.reduce((sum, item) => sum + item.totalPrice(), 0);
        if (this.discount) {
          total -= total * this.discount;
        }
        const orderHTML = `
          <div class="order-item">Total: $${total.toFixed(2)}</div>
          ${this.discount ? `<div class="order-item">Discount Applied: ${this.discount * 100}%</div>` : ''}
        `;
        document.getElementById('order-summary').innerHTML = orderHTML;
      }

      checkout() {
        let total = this.cart.reduce((sum, item) => sum + item.totalPrice(), 0);
        if (this.discount) {
          total -= total * this.discount;
        }
        alert(`Order placed! Total: $${total.toFixed(2)}\nMade by Preetham\nDiscord: progamerelite_3697794`);
        this.orders.push({ cart: [...this.cart], total });
        this.cart = [];
        this.renderCart();
        this.updateOrderSummary();
      }

      simulateOrder() {
        const productId = this.products[Math.floor(Math.random() * this.products.length)].id;
        this.addToCart(productId);
      }
    }

    const dropShipService = new DropShipService();
    dropShipService.renderProducts();

    // Expose functions to global scope
    function addToCart(productId) {
      dropShipService.addToCart(productId);
    }

    function applyCoupon() {
      dropShipService.applyCoupon();
    }

    function checkout() {
      dropShipService.checkout();
    }

    function simulateOrder() {
      dropShipService.simulateOrder();
    }
  </script>

</body>
</html>

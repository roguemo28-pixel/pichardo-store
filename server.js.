require("dotenv").config();
const express = require("express");
const Stripe = require("stripe");
const path = require("path");

const app = express();
const PORT = process.env.PORT || 4242;

if (!process.env.STRIPE_SECRET_KEY) {
  console.error("Falta STRIPE_SECRET_KEY en .env");
  process.exit(1);
}
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

// Reemplaza cada valor por el Price ID real de Stripe.
// Los precios se determinan aquí en el servidor, no desde el navegador.
const PRODUCTS = {
  "camiseta-oso": { priceId: "price_REEMPLAZAR_1", name: "Camiseta Oso PICHARDO" },
  "gorra-classic": { priceId: "price_REEMPLAZAR_2", name: "Gorra PICHARDO Classic" },
  "pantalon-urban": { priceId: "price_REEMPLAZAR_3", name: "Pantalón PICHARDO Urban" },
  "chaqueta-black": { priceId: "price_REEMPLAZAR_4", name: "Chaqueta PICHARDO Black" },
  "sueter-oso": { priceId: "price_REEMPLAZAR_5", name: "Suéter Oso PICHARDO" },
  "camiseta-gold": { priceId: "price_REEMPLAZAR_6", name: "Camiseta PICHARDO Gold" },
  "gorra-oso": { priceId: "price_REEMPLAZAR_7", name: "Gorra Oso PICHARDO" },
  "hoodie-bear": { priceId: "price_REEMPLAZAR_8", name: "Hoodie PICHARDO Bear" }
};

app.use(express.json());
app.use(express.static(path.join(__dirname, "public")));

app.post("/create-checkout-session", async (req,res)=>{
  try {
    const items = Array.isArray(req.body.items) ? req.body.items : [];
    if (!items.length) return res.status(400).json({error:"El carrito está vacío."});

    const line_items = items.map(item=>{
      const product = PRODUCTS[item.id];
      const quantity = Math.floor(Number(item.quantity));
      if (!product || !product.priceId.startsWith("price_") || !Number.isFinite(quantity) || quantity < 1 || quantity > 20) {
        throw new Error("Producto o cantidad inválida.");
      }
      return {price: product.priceId, quantity};
    });

    const baseUrl = process.env.PUBLIC_URL || `${req.protocol}://${req.get("host")}`;
    const session = await stripe.checkout.sessions.create({
      mode: "payment",
      line_items,
      billing_address_collection: "auto",
      shipping_address_collection: {allowed_countries:["US","DO"]},
      success_url: `${baseUrl}/success.html?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${baseUrl}/cancel.html`
    });

    res.json({url:session.url});
  } catch(error) {
    console.error(error);
    res.status(500).json({error:"No se pudo iniciar el pago. Revisa la configuración de Stripe."});
  }
});

app.listen(PORT, ()=>console.log(`PICHARDO: http://localhost:${PORT}`));

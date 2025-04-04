<script lang="ts">
  export let data: { products: { name: string; image: string; category: string }[] };

  const categories = [
    "Fruits, Seeds, Nuts",
    "Vegetables",
    "Greens",
    "Dairy",
    "Grains",
    "Legumes",
    "Specialty"
  ];

  const subcategories = {
    "Fruits, Seeds, Nuts": ["Fruits", "Seeds", "Nuts"]
  };

  let products = data.products; // Use the products data passed from +page.server.ts
  let selectedCategory = 'All Products';
  let showDropdown = false;
  let searchQuery = '';

  const categoryMap = Object.entries(subcategories).reduce((map, [parent, subs]) => {
    subs.forEach(sub => map[sub] = parent);
    return map;
  }, {} as Record<string, string>);

  // Filters products based on category and search query
  const filteredProducts = () => {
    return products.filter(product => {
      const parentCategory = categoryMap[product.category] || product.category;
      const matchesCategory = selectedCategory === 'All Products' || 
        selectedCategory === parentCategory || 
        selectedCategory === product.category;
      const matchesSearch = product.name.toLowerCase().includes(searchQuery.toLowerCase());
      return matchesCategory && matchesSearch;
    });
  };

  const toggleDropdown = () => {
    showDropdown = !showDropdown;
  };

  const selectSubcategory = (subcategory: string) => {
    selectedCategory = subcategory;
    showDropdown = false;
  };
</script>

<main class="max-w-5xl mx-auto pb-8">

<!-- Banner image -->
<section class="mb-6">
  <img src="/frontpage/fit-together.jpg" alt="Let's Get Fit Together" class="w-full">
</section>

<!-- CATALOGUE title and search Bar -->
<div class="flex justify-between items-center mb-6">
  <h2 class="text-3xl font-bold uppercase text-left">Catalogue</h2>
  <input
    type="text"
    placeholder="Search products..."
    bind:value={searchQuery}
    class="border border-gray-300 rounded-md p-2"
  />
</div>

<!-- Catalogue content -->
<div class="flex gap-6">

  <!-- Left Sidebar -->
  <aside class="w-1/4 bg-white p-6 shadow-md rounded-md self-start">
    <button class="py-2 hover:bg-gray-100 cursor-pointer underline" on:click={() => selectedCategory = 'All Products'} on:keydown={(e) => e.key === 'Enter' && (selectedCategory = 'All Products')}>
      All Products
    </button>
    <div class="mt-4">
      <ul>
        {#each categories as category}
          <li>
            <button class="py-2 hover:bg-gray-100 cursor-pointer category" on:click={() => category === 'Fruits, Seeds, Nuts' ? toggleDropdown() : selectedCategory = category} on:keydown={(e) => e.key === 'Enter' && (category === 'Fruits, Seeds, Nuts' ? toggleDropdown() : selectedCategory = category)} class:selected={selectedCategory === category}>
              {#if category === 'Fruits, Seeds, Nuts'}
                <span>{showDropdown ? '▼' : '▶'}</span>
              {/if}
              {category}
            </button>
            {#if category === 'Fruits, Seeds, Nuts'}
              <ul class="ml-4 subcategory-list" style="display: {showDropdown ? 'block' : 'none'};">
                {#each subcategories[category] as subcategory}
                  <li>
                    <button class="py-2 hover:bg-gray-100 cursor-pointer subcategory" on:click={() => selectSubcategory(subcategory)} on:keydown={(e) => e.key === 'Enter' && selectSubcategory(subcategory)} class:selected={selectedCategory === subcategory}>
                      {subcategory}
                    </button>
                    <hr class="subcategory-separator">
                  </li>
                {/each}
              </ul>
            {/if}
          </li>
          <hr class="category-separator">
        {/each}
      </ul>
    </div>
  </aside>

  <!-- Product Grid -->
  <section class="grid grid-cols-3 gap-6 w-3/4 items-start">
    {#if filteredProducts().length === 0}
      <p>No products found.</p>
    {:else}
      {#each filteredProducts() as product}
        <div class="bg-white p-4 text-center shadow-md rounded-md flex flex-col h-full product-box">
          <img
            class="w-32 mx-auto mb-4"
            src={`https://sabalancec-warehouse-sanv8.ondigitalocean.app/${product.image}`}
            alt={product.name}
          >
          <p class="mt-auto">{product.name}</p>
        </div>
      {/each}
    {/if}
  </section>

</div>

</main>

<style>

span {
  margin-right: 8px;
}
.category {
  font-weight: bold;
}
.subcategory {
  font-weight: normal;
}
.subcategory-list {
  border-top: 1px solid #e0e0e0;
  margin-top: 8px;
  padding-top: 8px;
}
hr {
  border: 0;
  border-top: 1px solid #e0e0e0;
  margin: 8px 0;
}
.category-separator {
  border-top: 2px solid #e0e0e0;
  margin: 8px 0;
}
.subcategory-separator {
  border-top: 1px solid #e0e0e0;
  margin: 4px 0;
}
.underline {
  text-decoration: underline;
}
.mt-4 {
  margin-top: 16px;
}

.grid {
  align-items: start;
  display: grid;
}
.flex {
  display: flex;
}
.flex-col {
  flex-direction: column;
}
.pb-8 {
  padding-bottom: 2rem;
}
.self-start {
  align-self: flex-start;
}
.h-full {
  height: auto; /* Prevent items from stretching unnecessarily */
}

/* Ensure consistent height for product boxes */
.product-box {
  height: 250px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.product-box img {
  max-height: 120px;
  object-fit: contain; /* Ensure images fit within their container */
  margin: 0 auto;
}

</style>
Adding {signal} to GET queries in the frontend means that if someone
leaves the page while the query is still loading it will abort the 
query.

    export const useProductPricesQuery = (productId: string) =>
    useQuery<ProductPrice[]>({
      queryKey: ["productPrices", productId],
      queryFn: async ({ signal }) => {
        const response = await axios.get(
          `/v1/product-prices/product/${productId}/`,
          {
            signal,
          },
        );
        return response.data.productPrices.map((price: unknown) =>
          newProductPrice(price),
        );
      },
    });

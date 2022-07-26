# TCGPlayerSDK

A sorta-typed SDK that barely works with Dataclasses and TCGPlayer's API all at once.

You might want to look at other projects for a more complete experience such as:

- https://github.com/zaxr/tcgplayer-api-client-python
- https://github.com/Buried-In-Code/TCGPlayer-Wrapper

This SDK only loosely implements a couple API functions right now.

To install this package from pip, try

`poetry add tcgplayersdk`
or
`pip install tcgplayersdk`

Good luck, and all bets are off on whether things work or not... I just rushed this onto git and pypi in a haze.
Consider this example script the only documentation for now :(

```python
if __name__ == '__main__':
    import os
    import logging

    from tcgplayersdk.category_enum import Categories
    from tcgplayersdk.sdk import TCGPlayerSDK
    from tcgplayersdk.util import sort_by_number

    tcgplayer = TCGPlayerSDK(
        os.getenv('PKMN_DECKLIST_TCGPLAYER_PUBLIC_KEY'),
        os.getenv('PKMN_DECKLIST_TCGPLAYER_PRIVATE_KEY'),
        categoryId=Categories.POKEMON,
        log_requests=logging.WARNING,
    )

    base_set = tcgplayer.get_products_in_group(604, get_extended_fields=True, include_skus=True)
    fossil = tcgplayer.get_products_in_group(630, get_extended_fields=True, include_skus=True)
    jungle = tcgplayer.get_products_in_group(635, get_extended_fields=True, include_skus=True)

    all_products = sort_by_number(base_set.results + fossil.results + jungle.results)

    skus = [card.get_sku([2], [10, 11, 122, 123]) for card in all_products]
    sku_prices = tcgplayer.get_prices_for_skus(skus)
    for card, sku in zip(all_products, sku_prices.results):
        print(f'{card.get_number()},{card.name},{sku.sku_id},{sku.lowest_listing_price},{card.url}')
```

To help with development, know that this project uses poetry, and a bunch of VSCode configuration. You should be able to
clone the repo, run `poetry install` and start hacking away.

# Extension Code Examples

## Admin UI Extension

```javascript
// extensions/admin/product-action/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation } from '@shopify/app-extensions-react';
import { Button, Banner, Text } from '@shopify/polaris';

export default function AdminExtension() {
  const applyRemoteOperation = useApplyRemoteOperation();
  const [isLoading, setIsLoading] = React.useState(false);

  const handleAction = async () => {
    setIsLoading(true);
    try {
      await applyRemoteOperation('process-product', { productId: '123' });
    } catch (error) {
      console.error('Error:', error);
    }
    setIsLoading(false);
  };

  return <Button onClick={handleAction} loading={isLoading}>Process</Button>;
}
```

## Checkout UI Extension

```javascript
// extensions/checkout-ui/shipping-info/src/Checkout.jsx
import React from 'react';
import { useApplyAttributeChange, Banner, Checkbox } from '@shopify/checkout-ui-extensions-react';

export default function CheckoutExtension() {
  const applyAttributeChange = useApplyAttributeChange();
  const [giftWrap, setGiftWrap] = React.useState(false);

  const handleGiftWrap = async (checked) => {
    setGiftWrap(checked);
    await applyAttributeChange({
      type: 'updateAttribute', key: 'gift_wrap', value: checked ? 'true' : 'false'
    });
  };

  return (
    <>
      <Banner status="info">Free shipping on orders over $50!</Banner>
      <Checkbox checked={giftWrap} onChange={handleGiftWrap}>Add gift wrapping (+$5)</Checkbox>
    </>
  );
}
```

## Theme App Extension (Liquid)

```liquid
{# blocks/announcement-bar.liquid #}
{% style %}
  .announcement-bar {
    background: {{ section.settings.bg_color }};
    color: {{ section.settings.text_color }};
    padding: 12px 20px;
    text-align: center;
  }
{% endstyle %}

<div class="announcement-bar">
  <p>{{ section.settings.message }}</p>
</div>

{% schema %}
{
  "name": "Announcement Bar",
  "target": "section",
  "settings": [
    { "type": "text", "id": "message", "label": "Message", "default": "Free shipping!" },
    { "type": "color", "id": "bg_color", "label": "Background", "default": "#000" },
    { "type": "color", "id": "text_color", "label": "Text", "default": "#fff" }
  ]
}
{% endschema %}
```

## POS UI Extension

```javascript
// extensions/pos/quick-discount/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation } from '@shopify/retail-ui-extensions';
import { Button, TextField, Stack } from '@shopify/polaris';

export default function POSDiscount() {
  const applyRemoteOperation = useApplyRemoteOperation();
  const [discount, setDiscount] = React.useState('10');

  const applyDiscount = async () => {
    await applyRemoteOperation('apply-discount', {
      amount: parseFloat(discount), type: 'percentage'
    });
  };

  return (
    <Stack vertical>
      <TextField label="Discount (%)" value={discount} onChange={setDiscount} type="number" />
      <Button onClick={applyDiscount} primary>Apply Discount</Button>
    </Stack>
  );
}
```

## Customer Account Extension

```javascript
// extensions/customer-account/order-notes/src/client.jsx
import React from 'react';
import { useApi } from '@shopify/customer-account-ui-extensions-react';
import { BlockStack, TextField, Button } from '@shopify/polaris';

export default function OrderNotes() {
  const api = useApi();
  const [notes, setNotes] = React.useState('');

  const saveNotes = async () => {
    await api.applyAttributeChange({
      type: 'updateAttribute', key: 'customer_notes', value: notes
    });
  };

  return (
    <BlockStack spacing="base">
      <TextField label="Notes" value={notes} onChange={setNotes} multiline={3} />
      <Button onClick={saveNotes}>Save</Button>
    </BlockStack>
  );
}
```

## Extension Configuration

```toml
# shopify.extension.toml
name = "My Extension"
type = "ui_extension"

[[extensions.targeting]]
target = "admin.product-list.action-menu-item"

[extensions.capabilities]
api_access = true
network_access = false

[[extensions.settings.fields]]
key = "enabled"
name = "Enable"
type = "boolean"
required = true
```

**See also:**
- [extension_types.md](extension_types.md) - All extension types
- [configuration.md](configuration.md) - TOML options

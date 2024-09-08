### Odoo Decorators

Odoo decorators are Python function decorators that allow you to extend or modify the behavior of methods in Odoo models. These decorators are commonly used to control access, manage transactional behavior, handle constraints, and optimize performance.

**What are Python Decorators?**
Decorators are a powerful feature in Python that allows you to modify the behavior of a function or method. They are often used to add functionality to existing code in a clean and readable way.

Below is a list of common Odoo decorators, their use cases, and examples, along with an explanation of how `self` behaves in each decorator.

---

### 1. **@api.model**
#### Use Case:
- Used when a method does not deal with a specific record set (i.e., operates at the model level).
- Methods decorated with `@api.model` are intended to be called without any specific record in mind, typically for creating records or performing model-wide operations.

#### **Behavior of `self`:**
- **`self` refers to the model**, not to any specific record. You are not operating on any recordset, but rather the model class itself.

#### Example:
```python
from odoo import models, api

class ExampleModel(models.Model):
    _name = 'example.model'

    @api.model
    def create_record(self, vals):
        """Creates a new record in the model"""
        return self.create(vals)

# Usage:
self.env['example.model'].create_record({'name': 'Sample'})
```

---

### 2. **@api.multi** (Deprecated in Odoo 13+)
#### Use Case:
- Used when a method is intended to be called on multiple records.
- Methods decorated with `@api.multi` operate over multiple records (i.e., it loops through the recordset).

#### **Behavior of `self`:**
- **`self` refers to a recordset**, meaning it can represent multiple records. You typically need to iterate over `self` using a `for` loop to apply the method logic to each record.

#### Example:
```python
from odoo import models, api

class ExampleModel(models.Model):
    _name = 'example.model'

    @api.multi
    def update_records(self):
        """Update all records in the current recordset"""
        for record in self:
            record.write({'field_name': 'new_value'})

# Usage:
records = self.env['example.model'].search([('field_name', '=', 'old_value')])
records.update_records()
```

> **Note:** In Odoo 14 and later, `@api.multi` is deprecated. It's better to use methods without this decorator, as Odoo methods automatically operate in multi-recordset mode.

---

### 3. **@api.depends**
#### Use Case:
- Defines a computed field method that depends on other fields.
- This decorator ensures that the field is recalculated when any of the dependent fields are changed.

#### **Behavior of `self`:**
- **`self` can represent either a single record or multiple records**. If the method is called in the context of multiple records, you need to iterate through `self`. For a single record, no iteration is needed.

#### Single Record Example:
```python
@api.depends('field1', 'field2')
def _compute_total(self):
    """Compute the total for a single record"""
    self.total = self.field1 + self.field2
```

#### Multiple Records Example:
```python
@api.depends('field1', 'field2')
def _compute_total(self):
    """Compute the total for multiple records"""
    for record in self:
        record.total = record.field1 + record.field2
```

---

### 4. **@api.constrains**
#### Use Case:
- Used to add custom validation logic on one or more fields.
- It raises a `ValidationError` if the constraint is not met.

#### **Behavior of `self`:**
- **`self` refers to a recordset**, meaning it can represent multiple records. You need to loop through `self` to apply the validation logic to each record.

#### Example:
```python
from odoo import models, fields, api
from odoo.exceptions import ValidationError

class ExampleModel(models.Model):
    _name = 'example.model'

    age = fields.Integer()

    @api.constrains('age')
    def _check_age(self):
        """Ensure the age is greater than 18"""
        for record in self:
            if record.age < 18:
                raise ValidationError("Age must be at least 18.")

# Usage:
record = self.env['example.model'].create({'age': 17})  # This will raise ValidationError
```

---

### 5. **@api.onchange**
#### Use Case:
- Executes a method when the value of a field changes in the view.
- Commonly used to dynamically modify field values or domain filters without saving the changes to the database.

#### **Behavior of `self`:**
- **`self` always represents a single record**. Onchange methods are triggered in the form view for a specific record, so no need to loop over `self`.

#### Example:
```python
from odoo import models, fields, api

class ExampleModel(models.Model):
    _name = 'example.model'

    product_id = fields.Many2one('product.product')
    price = fields.Float()

    @api.onchange('product_id')
    def _onchange_product_id(self):
        """Set the price when a product is selected"""
        if self.product_id:
            self.price = self.product_id.list_price

# Usage:
# When the product is changed in the view, the price will be automatically updated.
```

---

### 6. **@api.returns**
#### Use Case:
- Specifies the return type of a method.
- Useful when converting the return value to a specific type (such as a recordset, dictionary, or list).

#### **Behavior of `self`:**
- **`self` behaves normally**, just like the method type being decorated. It doesn't affect how `self` is handled but controls how the return value is formatted.

#### Example:
```python
from odoo import models, api

class ExampleModel(models.Model):
    _name = 'example.model'

    @api.model
    @api.returns('self', lambda value: value.id)
    def get_record_by_name(self, name):
        """Return a record based on its name"""
        return self.search([('name', '=', name)], limit=1)

# Usage:
record = self.env['example.model'].get_record_by_name('Sample')
```

---

### 7. **@api.model_create_multi**
#### Use Case:
- Optimizes the creation of multiple records at once by allowing bulk insertion.
- Used for performance optimization during record creation.

#### **Behavior of `self`:**
- **`self` refers to the model** (not to any specific record), similar to `@api.model`. You handle multiple records during creation.

#### Example:
```python
from odoo import models, api

class ExampleModel(models.Model):
    _name = 'example.model'

    @api.model_create_multi
    def create_multiple(self, vals_list):
        """Create multiple records at once"""
        return super(ExampleModel, self).create(vals_list)

# Usage:
records = self.env['example.model'].create_multiple([{'name': 'Record 1'}, {'name': 'Record 2'}])
```

---

### 8. **@api.returns and @api.v8**
#### Use Case:
- Used in older versions of Odoo (version 8) to manage return types and ensure backward compatibility between Odoo 8 and Odoo 9 APIs.

> These decorators are rarely used in modern Odoo versions.

---

### 9. **@api.autovacuum**
#### Use Case:
- Automatically triggered at regular intervals to perform maintenance tasks.
- Useful for background jobs like cleaning up records or updating caches.

#### **Behavior of `self`:**
- **`self` refers to the model**, as it typically operates on all records without focusing on a specific recordset.

#### Example:
```python
from odoo import models, api

class ExampleModel(models.Model):
    _name = 'example.model'

    @api.autovacuum
    def _cleanup_old_records(self):
        """Automatically clean up old records every interval"""
        old_records = self.search([('create_date', '<', fields.Datetime.now() - timedelta(days=30))])
        old_records.unlink()

# Usage:
# The method will automatically run at intervals to delete old records.
```
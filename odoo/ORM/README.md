# Odoo ORM Documentation

## Overview
Odoo ORM (Object-Relational Mapping) allows developers to interact with the database in an object-oriented manner, replacing raw SQL queries with Python objects. This abstraction ensures ease of use, maintainability, and a reduction in errors.

---

## Key Features of Odoo ORM:
1. **Automatic Table Creation**: Odoo ORM automatically creates corresponding database tables based on model definitions.
2. **Data Validation**: Ensures data integrity by validating data types and constraints.
3. **Relationships**: Supports one-to-many, many-to-one, and many-to-many relationships.
4. **Inheritance**: Allows extending existing models through inheritance.
5. **Querying**: Enables easy querying through Python methods without requiring SQL knowledge.

---

## Common ORM Methods

### CRUD Operations

#### **create(vals)**  
Creates a new record in the database.
```python
# Example: Creating a new product record
new_product = self.env['product.product'].create({
    'name': 'New Product',
    'list_price': 25.00,
    'default_code': 'PROD001'
})
```
**Output:**  
A new product with the specified name, price, and code will be created in the `product.product` model.

#### **read(fields)**  
Reads values from fields for specific records.
```python
# Example: Reading the name and price of a product
product = self.env['product.product'].browse(1)  # Assume product with ID 1
product_data = product.read(['name', 'list_price'])
```
**Output:**  
```python
[{'name': 'New Product', 'list_price': 25.00}]
```

#### **write(vals)**  
Updates existing records.
```python
# Example: Updating the price of a product
product = self.env['product.product'].browse(1)
product.write({'list_price': 30.00})
```
**Output:**  
The product with ID 1 will now have its price updated to 30.00.

#### **unlink()**  
Deletes records from the database.
```python
# Example: Deleting a product
product = self.env['product.product'].browse(1)
product.unlink()
```
**Output:**  
The product with ID 1 will be deleted.

---

### Searching and Browsing

#### **search(args)**  
Searches for records matching the specified domain.
```python
# Example: Searching for products with a price greater than 20
products = self.env['product.product'].search([('list_price', '>', 20)])
```
**Output:**  
A recordset containing products with prices greater than 20.

#### **search_count(args)**  
Counts the number of records matching a domain.
```python
# Example: Counting the number of products with a price greater than 20
product_count = self.env['product.product'].search_count([('list_price', '>', 20)])
```
**Output:**  
An integer representing the number of products with a price above 20.

#### **browse(ids)**  
Fetches records based on their IDs.
```python
# Example: Fetching products with IDs 1 and 2
products = self.env['product.product'].browse([1, 2])
```
**Output:**  
A recordset containing products with IDs 1 and 2.

---

### Context Management
- **with_context(context)**: Returns a new recordset with the specified context.
    ```python
    # Example: Return a new recordset with a specified context
    new_context = {'key': 'value'}
    records_with_context = self.env['model.name'].with_context(new_context).search([('field', '=', value)])

    # Example: Creating a product with a specific context
    context = {'lang': 'fr_FR'}
    product_with_context = self.env['product.product'].with_context(context).create({
        'name': 'Produit Français',
        'list_price': 35.00
    })
    # The newly created product will use the French context (e.g., French language for field labels).
    ```

- **with_prefetch(ids)**: Returns a new recordset with specified prefetch ids.
    ```python
    # Example: Return a new recordset with specified prefetch IDs
    prefetch_ids = [1, 2, 3]
    records_with_prefetch = self.env['model.name'].with_prefetch(prefetch_ids).search([('field', '=', value)])
    ```
- **with_env(env)**: Returns a new recordset with the specified environment.
    ```python
    # Example: Return a new recordset with a specified environment
    new_env = self.env(user=self.env.user, context={'key': 'value'})
    records_with_env = self.env['model.name'].with_env(new_env).search([('field', '=', value)])
    ```
- **with_user(user)**: Returns a new recordset with the specified user.
    ```python
    # Example: Return a new recordset with a specified user
    new_user = self.env.ref('base.user_admin')
    records_with_user = self.env['model.name'].with_user(new_user).search([('field', '=', value)])
    ```
- **sudo(user=None)**: Elevates privileges to the superuser or a specific user.
    ```python
    # Example: Elevate privileges to the superuser
    records_as_sudo = self.env['model.name'].sudo().search([('field', '=', value)])

    # Example: Elevate privileges to a specific user
    specific_user = self.env.ref('base.user_admin')
    records_as_sudo_user = self.env['model.name'].sudo(specific_user).search([('field', '=', value)])
    ```
---

### Record Conversion and Validation
- **_convert_to_write(vals)**: Converts record values into a format suitable for writing to the database.
    ```python
    # Example: Convert record values into a format suitable for writing
    values_to_convert = {'field1': 'value1', 'field2': 'value2'}
    converted_values = self.env['model.name']._convert_to_write(values_to_convert)
    # The converted_values can now be used to write to the database
    self.env['model.name'].create(converted_values)
    ```
- **_validate_fields(vals)**: Validates the values of fields according to field definitions.
    ```python
    # Example: Validate the values of fields according to field definitions
    values_to_validate = {'field1': 'value1', 'field2': 'value2'}
    # Validate the values, will raise ValidationError if invalid
    self.env['model.name']._validate_fields(values_to_validate)
    # If no error is raised, values can be safely used
    self.env['model.name'].create(values_to_validate)
    ```
    ```python
    # Example: Validating a new product's data
    values = {'name': 'Valid Product', 'list_price': 50.00}
    self.env['product.product']._validate_fields(values)
    ```
    **Output:**  
    If the values are invalid, a `ValidationError` will be raised.

---

### Data Loading and Import/Export
- **load(fields, data)**: Loads data into specified fields.
    ```python
    # Example: Load data into specified fields
    fields = ['field1', 'field2']
    data = [['value1_1', 'value1_2'], ['value2_1', 'value2_2']]
    # Load the data into the model
    result = self.env['model.name'].load(fields, data)
    # The result contains information about the import operation, such as ids of created records
    ```
- **import_data(fields, data, mode='init', current_module='')**: Imports data into the model.
    ```python
    # Example: Import data into the model
    fields = ['field1', 'field2']
    data = [['value1_1', 'value1_2'], ['value2_1', 'value2_2']]
    # Import the data into the model
    result = self.env['model.name'].import_data(fields, data, mode='init', current_module='')
    # The result contains information about the import operation
    ```
    ```python
    # Example: Importing product data
    fields = ['name', 'list_price']
    data = [['Imported Product', 45.00], ['Another Product', 60.00]]
    self.env['product.product'].import_data(fields, data)
    ```
    **Output:**  
    Two new products will be imported with the provided names and prices.
    
- **_import_rows(rows, fields)**: Imports data rows into the model.
    ```python
    # Example: Import data rows into the model
    fields = ['field1', 'field2']
    rows = [['value1_1', 'value1_2'], ['value2_1', 'value2_2']]
    # Import the data rows into the model
    self.env['model.name']._import_rows(rows, fields)
    # This is typically used internally by import_data
    ```
- **export_data(fields, raw_data=False)**: Exports data for the specified fields.
    ```python
    # Example: Export data for the specified fields
    fields = ['field1', 'field2']
    # Export the data for the fields
    result = self.env['model.name'].export_data(fields, raw_data=False)
    # The result contains the exported data
    exported_data = result['datas']
    ```

    ```python
    # Example: Exporting product data
    fields = ['name', 'list_price']
    exported_data = self.env['product.product'].export_data(fields)
    ```
    **Output:**  
    The exported data will be in the format:  
    ```python
    {'datas': [['Product A', 50.00], ['Product B', 70.00]]}
    ```

- **_import_record(data)**: Imports a single record.
    ```python
    # Example: Import a single record
    data = {'field1': 'value1', 'field2': 'value2'}
    # Import the single record into the model
    self.env['model.name']._import_record(data)
    # This method is typically used internally during data import operations
    ```

---

### Prefetching and Caching
- **_prefetch()**: Pre-fetches the records.
    ```python
    # Example: Pre-fetch records to optimize performance
    records = self.env['model.name'].search([('field', '=', value)])
    # Pre-fetch the records
    records._prefetch()
    # This method is usually used internally to optimize performance by preloading related records
    ```
    ```python
    # Example: Prefetching products
    products = self.env['product.product'].search([('list_price', '>', 20)])
    products._prefetch()
    ```
    **Output:**  
    Records will be prefetched, improving performance during future accesses.

- **_prefetch_ids**: Accesses the prefetch IDs of the recordset.
    ```python
    # Example: Access the prefetch IDs of the recordset
    records = self.env['model.name'].search([('field', '=', value)])
    # Get the prefetch IDs
    prefetch_ids = records._prefetch_ids
    # Prefetch IDs are used internally by Odoo to manage and optimize database access
    ```
- **invalidate_cache(ids, fields)**: Invalidates the cache for specified records and fields.
    ```python
    # Example: Invalidate the cache for specified records and fields
    records = self.env['model.name'].search([('field', '=', value)])
    # Invalidate the cache for these records and fields
    self.env['model.name'].invalidate_cache(ids=records.ids, fields=['field1', 'field2'])
    # This ensures that the next time these records and fields are accessed, they are reloaded from the database
    ```
    ```python
    # Example: Invalidating the cache for a product's price
    product = self.env['product.product'].browse(1)
    self.env['product.product'].invalidate_cache([1], ['list_price'])
    ```
    **Output:**  
    The product's price will be rel

- **flush()**: Flushes the pending updates to the database.
    ```python
    # Example: Flush the pending updates to the database
    records = self.env['model.name'].search([('field', '=', value)])
    # Perform some updates on the records
    records.write({'field1': 'new_value'})
    # Flush the pending updates to the database
    self.env['model.name'].flush()
    # This ensures that all pending changes are written to the database immediately
    ```

---

### Utility Methods
- **copy(default=None)**: Duplicates a record.
    ```python
    # Example: Duplicate a record
    record = self.env['model.name'].browse(1)
    # Duplicate the record
    duplicate_record = record.copy(default={'field': 'new_value'})
    # This creates a new record with the same values as the original, with optional overrides
    ```
- **copy_data(default=None)**: Retrieves data required to duplicate a record.
    ```python
    # Example: Retrieve data required to duplicate a record
    record = self.env['model.name'].browse(1)
    # Get the data required to duplicate the record
    duplicate_data = record.copy_data(default={'field': 'new_value'})
    # This returns a dictionary of field values that can be used to create a new record
    ```
- **fields_get(allfields=None, attributes=None)**: Retrieves metadata about the model's fields.
    ```python
    # Example: Retrieve metadata about the model's fields
    # Get metadata for all fields
    fields_metadata = self.env['model.name'].fields_get()

    # Get metadata for specific fields
    specific_fields_metadata = self.env['model.name'].fields_get(allfields=['field1', 'field2'])
    ```
- **fields_view_get(view_id=None, view_type='form', toolbar=False, submenu=False)**: Retrieves the structure of a view for a model.
    ```python
    # Example: Retrieve the structure of a form view for the model
    view_structure = self.env['model.name'].fields_view_get(view_id=None, view_type='form', toolbar=False, submenu=False)
    # The result contains the structure of the view, including fields and layout information
    ```
- **name_get()**: Returns a list of tuples containing the IDs and display names of records.
    ```python
    # Example: Get the display names of records
    records = self.env['model.name'].browse([1, 2, 3])
    # Get the display names
    display_names = records.name_get()
    # The result is a list of tuples (id, display_name)
    ```
- **name_create(name)**: Creates a new record with the given display name.
    ```python
    # Example: Create a new record with the given display name
    new_record = self.env['model.name'].name_create('New Record Name')
    # The result is the new record created with the specified name
    ```
- **default_get(fields)**: Retrieves default values for a list of fields.
    ```python
    # Example: Retrieve default values for a list of fields
    fields = ['field1', 'field2']
    # Get the default values
    default_values = self.env['model.name'].default_get(fields)
    # The result is a dictionary of field values
    ```
- **read_group(domain, fields, groupby)**: Performs a group by operation and returns aggregated results.
    ```python
    # Example: Perform a group by operation and return aggregated results
    domain = [('field', '=', 'value')]
    fields = ['field1', 'field2:sum']
    groupby = ['field1']
    # Get the aggregated results
    grouped_data = self.env['model.name'].read_group(domain, fields, groupby)
    # The result is a list of dictionaries with aggregated data
    ```
- **_read_flat(fields)**: Reads records in a flat format.
    ```python
    # Example: Read records in a flat format
    records = self.env['model.name'].search([('field', '=', 'value')])
    # Read the records in a flat format
    flat_data = records._read_flat(['field1', 'field2'])
    # The result is a list of dictionaries with the specified fields
    ```
---

### Navigation and Filtering
- **filtered(domain)**: Filters the recordset based on the provided domain.
- **filtered_domain(domain)**: Returns a recordset filtered by the given domain.
- **sorted(key, reverse=False)**: Sorts the recordset based on a key.
- **exists()**: Filters out records that have been deleted.

### Onchange and Change Tracking
- **onchange(values, field_name)**: Simulates an onchange event for the specified field.
- **modified(fields)**: Checks if the specified fields have been modified since the last read.
- **_modified_fields**: Returns the fields that have been modified.

### Business Logic and Workflow
- **_workflow_signal(signal)**: Sends a workflow signal to the records.
- **workflow_signal(signal)**: Sends a workflow signal to the records (deprecated in newer versions, use automated actions instead).
- **signal_workflow(signal)**: Sends a workflow signal to the records.
- **_action_open()**: Opens a form view of the record.

### Access Rights and Security
- **check_access_rights(operation, raise_exception=True)**: Checks if the user has specified access rights for the operation.
- **check_access_rule(operation)**: Checks if the user has access rules for the specified operation.
- **check_field_access_rights(operation, fields=None)**: Checks if the user has access rights for specified fields.
- **_apply_ir_rules(domain, mode='read')**: Applies record rules to the specified domain.
- **_check_company()**: Ensures records belong to the correct company.

### SQL Execution and Direct Operations
- **_cr**: Allows direct execution of SQL queries using the cursor.
- **_sql_select**: Executes a direct SQL SELECT statement.
- **_lock()**: Locks the records for update.

### Metadata and Reflection
- **_fields**: Accesses the fields of the model.
- **_inherits**: Accesses the inherited models of the current model.
- **_register_hook()**: Registers hooks for model changes.
- **_setup_base()**: Sets up the base structure of the model.
- **_group_by_full(domain, fields, groupby)**: Performs a full group by operation.

### Computed Fields and Dependencies
- **_compute(field_names)**: Manually triggers the computation of computed fields.
- **_compute_field(field_name)**: Computes the value of a computed field.
- **_compute_store()**: Computes and stores the values of computed fields.

### Record Lifecycle and ID Management
- **new(vals)**: Creates a new record without saving it to the database.
- **_external_id**: Provides access to the external ID of the record.
- **_get_external_id()**: Gets the external ID of the records.
- **_compute_ids()**: Computes the IDs of the records in the recordset.

### Data Management and Conversion
- **_convert_to_cache(vals, update=False)**: Converts values to the cache format.
- **_apply_default_values(vals)**: Applies default values to the fields of a record.
- **_convert_to_write(vals)**: Converts values to a format suitable for writing to the database.

### Miscellaneous Methods
- **_log_access()**: Logs access to the records.
- **_validate_fields(vals)**: Validates the values of fields according to field definitions.
- **_check_recursion()**: Checks for recursive relationships in a one2many or many2many field.
- **_action_open()**: Opens a form view of the record.

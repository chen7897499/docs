

rails 3.1.1
```rb
1.9.3-p551 :001 > ActionView::Helpers::FormBuilder.field_helpers
 => ["fields_for", "label", "text_field", "password_field", "hidden_field", "file_field", "text_area", "check_box", "radio_button", "search_field", "telephone_field", "phone_field", "url_field", "email_field", "number_field", "range_field"] 
1.9.3-p551 :002 > ActionView::Helpers::FormBuilder.field_helpers.length
 => 16 
```

rails 4.2.5
```rb
2.2.3 :004 > ActionView::Helpers::FormBuilder.field_helpers
 => [:fields_for, :label, :text_field, :password_field, :hidden_field, :file_field, :text_area, :check_box, :radio_button, :color_field, :search_field, :telephone_field, :phone_field, :date_field, :time_field, :datetime_field, :datetime_local_field, :month_field, :week_field, :url_field, :email_field, :number_field, :range_field] 
2.2.3 :005 > ActionView::Helpers::FormBuilder.field_helpers.length
 => 23 
```

rails 4自带了http://apidock.com/rails/v4.0.2/ActionView/Helpers/FormHelper/date_field
所以系统里的date_field要改成别的名字

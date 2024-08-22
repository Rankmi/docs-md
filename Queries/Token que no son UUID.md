
```ruby
uuid_regex_pattern = '^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$'

p = Axlsx::Package.new
workbook = p.workbook

Tenant.find_each do |tenant|
	Apartment::Tenant.switch(tenant.scheme) do
		invalid_users = User.where("token !~ ?", uuid_regex_pattern)
		next if invalid_users.empty?
		workbook.add_worksheet(name: tenant.name) do |sheet|
			sheet.add_row ["ID", "Name", "Email", "Token"]
			invalid_users.find_each do |user|
				sheet.add_row [user.id, user.name, user.email, user.token]
			end
		end
	end
end

timestamp = Time.now.strftime("%Y%m%d%H%M%S")
file_name = "invalid_tokens_report_#{timestamp}.xlsx"
file_path = Rails.root.join("tmp", file_name)
p.serialize(file_path.to_s)

puts "Report generated: #{file_path}"
```



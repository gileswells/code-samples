# Code Samples
### Fireball Run Admin Panel
For this project I needed to be able to rapidly build out admin screens. Using the CodeIgniter data models for db interaction I was able to create a form field wrapper to handle the html output in my views.

``` php
// This will create all the markup for an input field within the admin theme's standard markup.
// Will also fill in existing data if it already exists in the database.
function standard_input($common_name,$name,$required = false,$data = null){
	$output = '<p>';
	$output .= '<label ';
	$output .= ($required) ? 'class="required" ' : '' ;
	if(is_array($common_name)){
		$small = $common_name[1];
		$common_name = $common_name[0];
	}
	$output .= "for=\"$name\">$common_name:</label><br/>";
	$output .= "<input type=\"text\" id=\"$name\" class=\"full\" name=\"$name\" ";
	$output .= (isset($data->$name)) ? 'value="'.htmlentities($data->$name).'" ' : '' ;
	$output .= "/>";
	$output .= (isset($small)) ? '<br />'.$small : '';
	$output .= '</p>';
	return $output;
}

// Simple text field
echo standard_input('Field label', 'db_field_name', true, $dataObject);
```
![Field without description](https://raw.githubusercontent.com/gileswells/code-samples/master/images/field-without-description.png)
``` php
// Text field with additional help text below
echo standard_input(['Field label', 'Extra description'], 'db_field_name', true, $dataObject);
```
![Field with description](https://raw.githubusercontent.com/gileswells/code-samples/master/images/field-with-description.png)
``` php
// Other methods follow the same parameter pattern as above for different field types
standard_password();
standard_phone(); // Included JS to keep all phone numbers formatted properly
standard_file();
standard_textarea();
standard_tf(); // Radio buttons with Yes / No as options and 1 / 0 as values
standard_checkbox_tf(); // Checkbox for true with hidden input ahead of it with the false value to post through when not selected
standard_submit();
standard_date(); // HTML5
standard_email(); // HTML5
standard_url(); // HTML5
standard_number_input(); // HTML5

// This form field will grab the available enum values from the database and build a dropdown with those as options
standard_enum($common_name,$name,$table,$required = false,$data = null){
	$CI =& get_instance();
	$enums = $CI->data_model->get_enums($table,$name);
	$output = '<p>';
	$output .= '<label ';
	$output .= ($required) ? 'class="required" ' : '' ;
	if(is_array($common_name)){
		$small = $common_name[1];
		$common_name = $common_name[0];
	}
	$output .= "for=\"$name\">$common_name:</label><br/>";
	if(!isset($data->$name)){
		$output .= form_dropdown($name,$enums,false,'id="'.$name.'"');
	}else{
		$output .= form_dropdown($name,$enums,$data->$name,'id="'.$name.'"');
	}
	$output .= (isset($small)) ? '<br />'.$small : '';
	$output .= '</p>';
	return $output;
}
```

### Fireball Run Image upload handling
I needed a solution to allow for third parties to upload their own photos but still have them properly constrained to specific sizes per location on the site. Using this method for everything makes getting the files uploaded and resized very simple and reusable.

``` php
// Central method for uploading images and providing the required size to match the site template
protected function uploadFile($folder, $field, $filename, $width = null, $height = null) {
	if (isset($_FILES) && isset($_FILES[$field]) && $_FILES[$field]['name'] != '' && !empty($_FILES[$field]['tmp_name'])) {
		$uploadFolder = 'uploads/' . $folder . '/';
		$arr = explode(".", $_FILES[$field]['name']);
		$extension = $arr[count($arr) - 1];
		$filename = $filename . '.' . $extension;
		$fullPath = $uploadFolder . $filename;

		if (move_uploaded_file($_FILES[$field]['tmp_name'], $fullPath)) {
			if (isset($height) && isset($width)) {
				$resize = new resize($fullPath);
				$resize->resizeImage($width, $height, 'crop');
				$resize->saveImage($fullPath, 60);
			}
			return $filename;
		} else {
			return false;
		}
	}
	$this->optionalPhoto = true;
	return false;
}

// Usage is just a single method.
$uploadedFile = $this->uploadFile('sidebar/large', 'photo', $insertId, 600, 400);
if ($uploadedFile)
	$formData['photo'] = $uploadedFile;

$this->db->where('id', $id)->update($table, $formData);
```

## Projects

### [EASports.com homepage](https://www.easports.com/)
I was the primary developer on this project which had to include multiple levels of interaction and multiple resolutions to support mobile, table, and desktop. The core of the development was done in about 2 weeks with tweaks and fine tuning taking another few weeks till it was just right.
### [EASports.com Ask NHL](https://www.easports.com/nhl/ask)
I was the primary developer handling the front and back of this feature. This single page application took user's questions and would allow the game's producers to answer them with images, videos or just text on a custom admin panel build with bootstrap.
### [EASports.com Social Sharing](https://www.easports.com/fifa/features)
I created the new functionality for the social sharing widget on all the marketing pages for easports.com. The widget is intended to draw on the iconography of sharing on mobile devices and fixed the problem of sharing not being visible at all times on mobile.
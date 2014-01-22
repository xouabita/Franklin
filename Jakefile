var fs     = require('fs');
var JSZip  = require('node-zip');
var jade   = require('jade');
var stylus = require('stylus');
var path   = require('path');

function makeZip(dir, zip) {
	var files = fs.readdirSync(dir);
	files.forEach(function (file) {
		var real = path.join(dir,file);
		if (fs.lstatSync(real).isFile()) {
			// Switch extension
			switch (file.split('.').pop()) {
				case "jade":
					// Jade Render
					var html = jade.renderFile(real, {});
					var out  = file.substr(0, file.lastIndexOf('.')) + ".html";
					zip.file(out, html);
					break;
				case "stylus":
					// Stylus Render
					var styl = fs.openSync(real, 'r');
					var out  = file.substr(0, file.lastIndexOf('.')) + ".stylus";
					stylus.render(styl, {}, function(err, css) {
						if (err) throw err;
						zip.file(out, css);
					});
					break;
				default:
					// Just add the file to the zip
					var data = fs.openSync(real, 'r');
					zip.file(file, data);
					break;
			}
		} else {
			makeZip(real, zip.folder(file));
		}
	});
}

desc('Build the src folder into .nw');
task('build', function (params) {
	var zip  = new JSZip();
	makeZip('./src/', zip);
	var data = zip.generate();
	fs.writeFileSync('test.zip', data, 'binary');
});
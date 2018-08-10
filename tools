// ==UserScript==
// @name         Census of Canada Links
// @namespace    https://github.com/yulvil/
// @version      0.1
// @description  Add links to navigate census records
// @author       yulvil
// @match        https://www.bac-lac.gc.ca/eng/census/*/Pages/item.aspx?itemid=*
// @grant        none
// ==/UserScript==

(function () {
	'use strict';

	var re_year = /\/(\d{4})\//;
	var yyyy = re_year.exec(document.location) || {};
	var re_lang = /\/(eng|fra)\//;
	var lang = re_lang.exec(document.location) || {};

	var provinces = {
		"Alberta": "AB",
		"British Columbia": "BC",
		"Manitoba": "MB",
		"New Brunswick": "NB",
		"Northwest Territories": "NT",
		"Nova Scotia": "NS",
		"Ontario": "ON",
		"Prince Edward Island": "PE",
		"Quebec": "QC",
		"Saskatchewan": "SK",
		"Yukon": "YT",

		"Colombie-Britannique": "BC",
		"&#206;le-du-Prince-&#201;douard": "PE",
		"Nouveau-Brunswick": "NB",
		"Nouvelle-&#201;cosse": "NS",
		"Qu&#233;bec": "QC",
		"Territoires du Nord-Ouest": "NT",

		"Canada East (Quebec)": "CE",
		"Canada West (Ontario)": "CW"
	};

	var fields = {
		"Surname": "cnsSurname",
		"Province": "cnsProvinceCode",
		"District Name": "cnsDistrictNameEn",
		"District Number": "cnsDistrictNum",
		"Sub-District Name": "cnsSubdistrictNameEn",
		"Sub-District Number": "cnsSubdistrictNum",
		"Division Number": "cnsDivisionNum",
		"Page Number": "cnsPageNum"
	};

	var model1 = {
		"values": {
			"cnsSurname": {
				"q": "cnsSurname,cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum,cnsPageNum"
			},
			"cnsProvinceCode": {
				"q": "cnsProvinceCode"
			},
			"cnsDistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNameEn"
			},
			"cnsDistrictNum": {
				"q": "cnsProvinceCode,cnsDistrictNum"
			},
			"cnsSubdistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNameEn"
			},
			"cnsSubdistrictNum": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum"
			},
			"cnsPageNum": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum,cnsPageNum"
			}
		}
	};

	var model2 = {
		"values": {
			"cnsSurname": {
				"q": "cnsSurname,cnsProvinceCode,cnsDistrictNameEn,cnsSubdistrictNameEn,cnsPageNum"
			},
			"cnsProvinceCode": {
				"q": "cnsProvinceCode"
			},
			"cnsDistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNameEn"
			},
			"cnsSubdistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNameEn,cnsSubdistrictNameEn"
			},
			"cnsPageNum": {
				"q": "cnsProvinceCode,cnsDistrictNameEn,cnsSubdistrictNameEn,cnsPageNum"
			}
		}
	};

	var model3 = {
		"values": {
			"cnsSurname": {
				"q": "cnsSurname,cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum,cnsPageNum"
			},
			"cnsProvinceCode": {
				"q": "cnsProvinceCode"
			},
			"cnsDistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNameEn"
			},
			"cnsDistrictNum": {
				"q": "cnsProvinceCode,cnsDistrictNum"
			},
			"cnsSubdistrictNameEn": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNameEn"
			},
			"cnsSubdistrictNum": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum"
			},
			"cnsDivisionNum": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum,cnsDivisionNum"
			},
			"cnsPageNum": {
				"q": "cnsProvinceCode,cnsDistrictNum,cnsSubdistrictNum,cnsDivisionNum,cnsPageNum"
			}
		}
	};

	var m = {};
	m["1851/eng"] = model1;
	m["1861/eng"] = model2;
	m["1871/eng"] = model1;
	m["1881/eng"] = model3;
	m["1891/eng"] = model1;
	m["1901/eng"] = model3;
	m["1911/eng"] = model1;
	m["1921/eng"] = model1;
	m["1931/eng"] = model1;

	var map = {};

	var values = m[yyyy[1] + "/" + lang[1]].values;

	var elements = document.getElementsByTagName("strong");
	for (var i = 0; i < elements.length; i++) {
		var key = elements[i].innerText.split(":")[0];
		if (values[fields[key]]) {
			(values[fields[key]])["value"] = elements[i].parentNode.innerText.split(":")[1].trim();
			(values[fields[key]])["node"] = elements[i].parentNode;
		}
	}

	function getLink(query, values, year, pageDiff) {
		var link = "http://www.bac-lac.gc.ca/eng/census/" + year + "/Pages/results.aspx?k=";
		var fields = query.split(",");
		for (var i = 0; i < fields.length; i++) {
			if (i > 0) {
				link += ' AND ';
			}
			var value = (values[fields[i]])["value"];
			if (fields[i] === "cnsProvinceCode") {
				value = provinces[value];
			} else if (fields[i] === "cnsPageNum" && pageDiff != 0) {
				if (value.indexOf('-') != -1) {
					var pageNums = value.split("-");
					if (pageDiff == -1) {
						value = (parseInt(pageNums[0]) - 2) + "-" + (parseInt(pageNums[0]) - 1);
					} else {
						value = (parseInt(pageNums[1]) + 1) + "-" + (parseInt(pageNums[1]) + 2);
					}
				} else {
					value = parseInt(value) + pageDiff;
				}
			}

			link += fields[i] + '="' + value + '"';
		}

		return encodeURI(link);
	}

	var res = "";
	for (var k in values) {
		if (k === "cnsPageNum") {
			var link = getLink(values[k].q, values, yyyy[1], -1);
			values[k].node.innerHTML += ' <a href="' + link + '">Prev</a>';
		}

		link = getLink(values[k].q, values, yyyy[1], 0);
		values[k].node.innerHTML += ' <a href="' + link + '">Link</a>';

		if (k === "cnsPageNum") {
			link = getLink(values[k].q, values, yyyy[1], 1);
			values[k].node.innerHTML += ' <a href="' + link + '">Next</a>';
		}
	}
})();

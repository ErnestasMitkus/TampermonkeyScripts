// ==UserScript==
// @name         SIS WOS Helper
// @version      1.0
// @description  SIS WOS Helper
// @author       Ernestas Mitkus
// @match        http://localhost:9000/*
// @grant        none
// ==/UserScript==

const DEFAULT_VIEWS = {
  FF_FORM: 'FF_FORM',
  FF_PRICE: 'FF_PRICE',
  PRE_RACE: 'PRE_RACE',
  IN_RACE: 'IN_RACE',
  PREVIEW: 'PREVIEW',
  CREDIT_CARD_RESULT: 'CREDIT_CARD_RESULT',
  PROVISIONAL_RESULT: 'PROVISIONAL_RESULT',
  PROVISIONAL_PHOTO: 'PROVISIONAL_PHOTO',
  PROVISIONAL_WINNER: 'PROVISIONAL_WINNER',
  PUNDITS_TIPS: 'PUNDITS_TIPS',
  FF_RESULT: 'FF_RESULT',
  SPLIT_SCREEN: 'SPLIT_SCREEN',
  NO_EVENT: 'NO_EVENT',
  NO_OAN: 'NO_OAN',
  TIMEFORM_FLAGS: 'TIMEFORM_FLAGS',
  TIMEFORM_SELECTIONS: 'TIMEFORM_SELECTIONS',
  TIMEFORM_KEY_STATS: 'TIMEFORM_KEY_STATS',
  RUNNERS_AND_RIDERS: 'RUNNERS_AND_RIDERS',
  SERVICE_SCHEDULE: 'SERVICE_SCHEDULE',
  WELCOME: 'WELCOME',
  OFFICIAL_PHOTO: 'OFFICIAL_PHOTO'
};
const LS_VIEWS_KEY = 'sis.wow.helper.views';

function lsSaveViews(views) {
    return window.localStorage.setItem(LS_VIEWS_KEY, JSON.stringify(views));
}

function lsGetViews() {
    return JSON.parse(window.localStorage.getItem(LS_VIEWS_KEY)) || {};
}

if (Object.keys(lsGetViews()).length === 0) {
  lsSaveViews(DEFAULT_VIEWS);
}

function viewSelect() {
    return document.getElementById("WOSHelper_View");
}

function addJQuery(callback) {
    let script = document.createElement("script");
    script.setAttribute("src", "https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js");
    script.type = "text/javascript";
    script.addEventListener("load", function() {
        let jQuery_321 = $.noConflict(true);
        callback(jQuery_321);
    }, false);
    document.getElementsByTagName("head")[0].appendChild(script);
}

function addEvent(element, eventName, callback) {
    if (element.addEventListener) {
        element.addEventListener(eventName, callback, false);
    } else if (element.attachEvent) {
        element.attachEvent("on" + eventName, callback);
    } else {
        element["on" + eventName] = callback;
    }
}

function getParameterByName(name, url) {
    if (!url) url = window.location.href;
    name = name.replace(/[\[\]]/g, '\\$&');
    let regex = new RegExp('[?&]' + name + '(=([^&#]*)|&|#|$)'),
        results = regex.exec(url);
    if (!results) return null;
    if (!results[2]) return '';
    return decodeURIComponent(results[2].replace(/\+/g, ' '));
}

function wosHelper($) {
    let WOS_CONFIG = {
        regions: [1, 2, 3, 4, 5, 6, 7, 8, 9].reduce((acc, curr) => {acc["REG" + curr] = "REG" + curr; return acc;}, {}),
        views: lsGetViews()
    };

    function createPanel() {
        const el = document.createElement("div");
        el.style.background = "#61ceab";
        el.style.position = "absolute";
        el.style.width = "300px";
        el.style.height = "0";
        el.style.right = "3em";
        el.style.top = "3em";
        el.style.opacity = "0";
        el.style.transition = "opacity 0.7s ease-out, height 0.7s ease-out";
        el.style.overflow = "hidden";
        el.style.color = "#000";
        el.style.fontSize = "24px";
        el.style.padding = "15px 10px";
        el.style.fontFamily = "Verdana, Geneva, sans-serif";

        document.body.appendChild(el);
        return el;
    }

    function createSelection(title, options) {
        const container = document.createElement("div");
        container.style.width = "100%";
        container.style.marginBottom = "10px";

        const label = document.createElement("label");
        label.innerText = title;
        container.appendChild(label);

        const selection = document.createElement("select");
        selection.id = "WOSHelper_" + title;
        selection.style.float = "right";
        selection.style.width = "150px";
        selection.style.padding = "3px";

        for (const opt in options) {
            const option = document.createElement("option");
            option.text = opt;
            option.value = options[opt];
            selection.appendChild(option);
        }

        const currentOption = getParameterByName(title.toLowerCase(), location.href);
        if (currentOption != null) {
            selection.value = currentOption;
        }

        container.appendChild(selection);
        return container;
    }

    function createButton(title, action) {
        const container = document.createElement("div");
        container.style.textAlign = "center";
        container.style.width = "100%";
        container.style.marginTop = "30px";

        const button = document.createElement("button");
        button.innerText = title;
        button.onclick = action;

        container.appendChild(button);
        return container;
    }

    function createAddViewButton() {
        const btn = createButton('+', e => {
            const viewName = (prompt("Enter new view name") || '').toUpperCase();
            const views = lsGetViews();
            if (viewName === '') {
                alert("View can't be empty");
                return;
            }
            if (views[viewName] !== undefined) {
                alert("View already exists");
                return;
            }
            views[viewName] = viewName;
            lsSaveViews(views);
            WOS_CONFIG = lsGetViews();

            const option = document.createElement("option");
            option.text = viewName;
            option.value = viewName;
            viewSelect().appendChild(option);
        });
        btn.style.width = "36px";
        btn.style.height = "36px";
        btn.style.display = "inline-block";
        btn.style.marginRight = "5px";

        btn.children[0].style.width = "36px";
        btn.children[0].style.height = "36px";
        return btn;
    }

    function createRemoveViewButton() {
        const btn = createButton('-', e => {
            const selection = viewSelect();
            const views = lsGetViews();
            const viewName = selection.value;
            if (viewName === undefined) {
                alert('No view selected');
                return;
            }
            if (views[viewName] !== undefined) {
                delete views[viewName];
                lsSaveViews(views);
                WOS_CONFIG = lsGetViews();
                selection.remove(selection.selectedIndex);
            }
        });
        btn.style.width = "36px";
        btn.style.height = "36px";
        btn.style.display = "inline-block";
        btn.style.marginRight = "5px";

        btn.children[0].style.width = "36px";
        btn.children[0].style.height = "36px";
        return btn;
    }

    function createListEventsButton() {
        return createButton("List Events", e => {
            if (typeof $r === 'undefined') {
                console.log("React Store is not defined.");
                return;
            }
            const events = (($r.store.getState() || {}).sportsData || {}).events || {};

            console.log('Total events found: ' + Object.keys(events).length);

            let counter = 0;
            for (let eventId in events) {
                const event = events[eventId]
                if (event.categoryCode ==='HR' && event.country !== 'VR') {
                    console.log(++counter, {
                        id: event.id,
                        country: event.country,
                        categoryCode: event.categoryCode,
                        time: event.time,
                        venue: event.venue,
                        selectionsSize: (event.selections || []).length,
                        result: event.result
                    });
                }
            }
        });
    }

    function createSubmitButton() {
        return createButton("Submit", e => {
            const region = document.getElementById("WOSHelper_Region").value;
            const view = viewSelect().value;

            let url = location.href;

            // region
            if (url.indexOf("region") > 0) {
                const curr = getParameterByName("region");
                url = url.replace(curr, region);
            } else {
                url += url.indexOf("?") > 0 ? "&" : "?";
                url += "region=" + region;
            }

            // view
            if (url.indexOf("view") > 0) {
                const curr = getParameterByName("view");
                url = url.replace(curr, view);
            } else {
                url += url.indexOf("?") > 0 ? "&" : "?";
                url += "view=" + view;
            }

            location.href = url;
        });
    }

    const panel = createPanel();
    panel.appendChild(createSelection("Region", WOS_CONFIG.regions));
    panel.appendChild(createSelection("View", WOS_CONFIG.views));

    panel.appendChild(createAddViewButton());
    panel.appendChild(createRemoveViewButton());

    panel.appendChild(createListEventsButton());
    panel.appendChild(createSubmitButton());

    addEvent(document, "keypress", function (e) {
        e = e || window.event;
        if (e.shiftKey && e.keyCode === "W".charCodeAt(0)) {
            // toggle visibility
            const isVisible = panel.style.opacity !== "0";
            panel.style.opacity = isVisible ? "0" : "1";
            panel.style.height = isVisible ? "0" : "400px";
        }
    });
}

addJQuery(wosHelper);



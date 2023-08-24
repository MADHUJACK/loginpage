# loginpage
import throttle from 'lodash.throttle';
import debounce from 'lodash.debounce';

import observe from './libs/observer';
import observer from './libs/observer';

import detect from './helpers/detector';
import handleScroll from './helpers/handleScroll';
@@ -139,13 +139,25 @@ const init = function init(settings) {
  // Create initial array with elements -> to be fullfilled later with prepare()
  $aosElements = elements();

  /**
   * Disable mutation observing if not supported
   */
  if (!options.disableMutationObserver && !observer.isSupported()) {
    console.info(`
      aos: MutationObserver is not supported on this browser,
      code mutations observing has been disabled.
      You may have to call "refreshHard()" by yourself.
    `);
    options.disableMutationObserver = true;
  }

  /**
   * Observe [aos] elements
   * If something is loaded by AJAX
   * it'll refresh plugin automatically
   */
  if (!options.disableMutationObserver) {
    observe('[data-aos]', refreshHard);
    observer.ready('[data-aos]', refreshHard);
  }

  /**
  45 changes: 27 additions & 18 deletions45  
src/js/libs/observer.js
@@ -20,23 +20,6 @@ function containsAOSNode(nodes) {
  return false;
}

function ready(selector, fn) {
  const doc = window.document;
  const MutationObserver =
    window.MutationObserver ||
    window.WebKitMutationObserver ||
    window.MozMutationObserver;

  const observer = new MutationObserver(check);
  callback = fn;

  observer.observe(doc.documentElement, {
    childList: true,
    subtree: true,
    removedNodes: true
  });
}

function check(mutations) {
  if (!mutations) return;

@@ -51,4 +34,30 @@ function check(mutations) {
  });
}

export default ready;
function getMutationObserver() {
  return (
    window.MutationObserver ||
    window.WebKitMutationObserver ||
    window.MozMutationObserver
  );
}

function isSupported() {
  return !!getMutationObserver();
}

function ready(selector, fn) {
  const doc = window.document;
  const MutationObserver = getMutationObserver();

  const observer = new MutationObserver(check);
  callback = fn;

  observer.observe(doc.documentElement, {
    childList: true,
    subtree: true,
    removedNodes: true
  });
}

export default { isSupported, ready };

/*
 * FoE Helper 4.8.7.0 add-on: Placed Buildings register
 * Added as a separate module; existing modules are not modified.
 *
 * The production fields use FoE Helper's existing calculation routines.
 * Figures are modelled building outputs, not guaranteed collection results.
 */
let PlacedBuildings = {
    SkipTypes: ['off_grid', 'outpost_ship', 'hub_main', 'hub_part', 'friends_tavern'],
    Data: [],
    State: {
        query: '',
        eras: new Set(),
        types: new Set(),
        productions: new Set(),
        productionMode: 'any',
        sort: 'name',
        direction: 'asc',
        group: 'none'
    },

    Text: {
        en: {
            title: 'Placed Buildings', description: 'Browse and filter all structures occupying tiles in your main city.',
            search: 'Search by name, age, type or entity ID…', refresh: 'Refresh', age: 'Ages', types: 'Building types',
            production: 'Production filters', match: 'Match selected production filters', any: 'Any selected', all: 'All selected',
            sort: 'Sort by', direction: 'Order', asc: 'Ascending', desc: 'Descending', group: 'Group by', none: 'No grouping',
            name: 'Name', era: 'Age', type: 'Type', footprint: 'Footprint', tiles: 'Tiles', fp: 'Forge points',
            coins: 'Coins', supplies: 'Supplies', goods: 'Goods (all ages)', guildGoods: 'Guild goods', units: 'Units',
            specialGoods: 'Special goods', medals: 'Medals', diamonds: 'Diamonds', population: 'Population', happiness: 'Happiness',
            rating: 'Efficiency score', position: 'Position', buildings: 'buildings', occupied: 'building tiles (sum of footprints)',
            noResults: 'No buildings match the selected filters.', noData: 'No placed city data is available yet.',
            productionNote: 'Production values are calculated by FoE Helper from building data. They are estimates/model values, not guaranteed collection results or a common per-hour rate.',
            locate: 'Locate', locateUnavailable: 'Building highlighting is unavailable in this browser.',
            street: 'street', unknown: 'Unknown',
        },
        fr: {
            title: 'Bâtiments placés', description: 'Consulter et filtrer les structures occupant des cases dans votre ville principale.',
            search: 'Rechercher par nom, âge, type ou identifiant…', refresh: 'Actualiser', age: 'Ères', types: 'Types de bâtiments',
            production: 'Filtres de production', match: 'Correspondance des filtres de production', any: 'Au moins un', all: 'Tous',
            sort: 'Trier par', direction: 'Ordre', asc: 'Croissant', desc: 'Décroissant', group: 'Regrouper par', none: 'Aucun regroupement',
            name: 'Nom', era: 'Ère', type: 'Type', footprint: 'Dimensions', tiles: 'Cases', fp: 'Points Forge',
            coins: 'Pièces', supplies: 'Marchandises', goods: 'Biens (toutes ères)', guildGoods: 'Biens de guilde', units: 'Unités',
            specialGoods: 'Biens spéciaux', medals: 'Médailles', diamonds: 'Diamants', population: 'Population', happiness: 'Bonheur',
            rating: 'Score d’efficacité', position: 'Position', buildings: 'bâtiments', occupied: 'cases de bâtiments (somme des empreintes)',
            noResults: 'Aucun bâtiment ne correspond aux filtres sélectionnés.', noData: 'Les données de la ville ne sont pas encore disponibles.',
            productionNote: 'Les productions sont calculées par FoE Helper à partir des données des bâtiments. Ce sont des estimations, pas des résultats de collecte garantis ni un taux horaire commun.',
            locate: 'Localiser', locateUnavailable: 'La mise en évidence des bâtiments n’est pas disponible dans ce navigateur.',
            street: 'rue', unknown: 'Inconnu',
        },
        de: {
            title: 'Platzierte Gebäude', description: 'Alle Gebäude und Strukturen auf den Feldern deiner Hauptstadt durchsuchen und filtern.',
            search: 'Nach Name, Zeitalter, Typ oder Entity-ID suchen…', refresh: 'Aktualisieren', age: 'Zeitalter', types: 'Gebäudetypen',
            production: 'Produktionsfilter', match: 'Übereinstimmung der Produktionsfilter', any: 'Beliebige Auswahl', all: 'Alle Auswahlen',
            sort: 'Sortieren nach', direction: 'Reihenfolge', asc: 'Aufsteigend', desc: 'Absteigend', group: 'Gruppieren nach', none: 'Keine Gruppierung',
            name: 'Name', era: 'Zeitalter', type: 'Typ', footprint: 'Grundfläche', tiles: 'Felder', fp: 'Forge-Punkte',
            coins: 'Münzen', supplies: 'Vorräte', goods: 'Güter (alle Zeitalter)', guildGoods: 'Gildengüter', units: 'Einheiten',
            specialGoods: 'Spezialgüter', medals: 'Medaillen', diamonds: 'Diamanten', population: 'Bevölkerung', happiness: 'Zufriedenheit',
            rating: 'Effizienzwert', position: 'Position', buildings: 'Gebäude', occupied: 'Gebäudefelder (Summe der Grundflächen)',
            noResults: 'Keine Gebäude entsprechen den gewählten Filtern.', noData: 'Noch keine Stadtdaten verfügbar.',
            productionNote: 'Die Produktionswerte werden von FoE Helper anhand der Gebäudedaten berechnet. Es sind Schätz-/Modellwerte, keine garantierten Sammelergebnisse und keine einheitliche Stundenrate.',
            locate: 'Markieren', locateUnavailable: 'Die Gebäudemarkierung ist in diesem Browser nicht verfügbar.',
            street: 'Straße', unknown: 'Unbekannt',
        },
        tr: {
            title: 'Yerleştirilmiş Binalar', description: 'Ana şehrinizde alan kaplayan tüm yapıları görüntüleyin ve filtreleyin.',
            search: 'Ad, çağ, tür veya varlık kimliğine göre ara…', refresh: 'Yenile', age: 'Çağlar', types: 'Bina türleri',
            production: 'Üretim filtreleri', match: 'Seçilen üretim filtrelerini eşleştir', any: 'Seçilenlerden herhangi biri', all: 'Seçilenlerin tümü',
            sort: 'Sıralama', direction: 'Sıra', asc: 'Artan', desc: 'Azalan', group: 'Grupla', none: 'Gruplama yok',
            name: 'Ad', era: 'Çağ', type: 'Tür', footprint: 'Ölçü', tiles: 'Alan', fp: 'Forge puanı',
            coins: 'Para', supplies: 'Erzak', goods: 'Mallar (tüm çağlar)', guildGoods: 'Lonca malları', units: 'Birimler',
            specialGoods: 'Özel mallar', medals: 'Madalya', diamonds: 'Elmas', population: 'Nüfus', happiness: 'Mutluluk',
            rating: 'Verimlilik puanı', position: 'Konum', buildings: 'bina', occupied: 'bina alanı (taban alanları toplamı)',
            noResults: 'Seçilen filtrelere uyan bina bulunamadı.', noData: 'Henüz şehir verisi mevcut değil.',
            productionNote: 'Üretim değerleri FoE Helper tarafından bina verilerinden hesaplanır. Bunlar tahmini/model değerleridir; garanti edilmiş toplama sonuçları veya ortak bir saatlik üretim oranı değildir.',
            locate: 'Haritada göster', locateUnavailable: 'Bu tarayıcıda bina işaretleme kullanılamıyor.',
            street: 'yol', unknown: 'Bilinmiyor',
        }
    },

    t(key) {
        const selected = String(localStorage.getItem('user-language') || (typeof GuiLng !== 'undefined' ? GuiLng : 'en') || 'en').toLowerCase();
        const language = selected.split('-')[0];
        return (this.Text[language] || this.Text.en)[key] || this.Text.en[key] || key;
    },

    init: () => {
        if ($('#placedbuildings').length > 0) {
            HTML.CloseOpenBox('placedbuildings');
            return;
        }

        HTML.AddCssFile('placedBuildings');
        HTML.Box({
            id: 'placedbuildings',
            title: PlacedBuildings.t('title'),
            auto_close: true,
            dragdrop: true,
            resize: true,
            minimize: true,
            popout: () => MainParser.PopOut('placedbuildings', 1250, 650)
        });

        PlacedBuildings.ReadData();
        PlacedBuildings.Render();
    },

    ReadData: () => {
        const rawBuildings = Object.values(MainParser.CityMapData || {});
        const entities = MainParser.CityEntities || {};
        const rows = [];

        for (const raw of rawBuildings) {
            const meta = entities[raw.cityentity_id];
            if (!meta || PlacedBuildings.SkipTypes.includes(meta.type)) continue;

            let footprint;
            try {
                footprint = CityMap.GetBuildingSize(raw);
            } catch (error) {
                continue;
            }

            const area = Number(footprint?.building_area) || 0;
            if (area <= 0) continue;

            let era = PlacedBuildings.t('unknown');
            try {
                era = Technologies.getEraName(raw.cityentity_id, raw.level) || CurrentEra || PlacedBuildings.t('unknown');
            } catch (error) {}

            let building = null;
            try {
                building = CityBuildings.createBuilding(meta, era, raw, true);
            } catch (error) {
                // Keep the placed structure in the register even if a special entity
                // cannot be handled by the full production parser.
            }

            if (!building) {
                building = {
                    id: raw.id,
                    entityId: raw.cityentity_id,
                    name: meta.name || raw.cityentity_id,
                    type: meta.type || 'unknown',
                    eraName: era,
                    population: 0,
                    happiness: 0,
                    production: [],
                    boosts: [],
                    rating: null,
                    size: { width: footprint.xsize || 0, length: footprint.ysize || 0 }
                };
            }

            const values = PlacedBuildings.ReadProduction(building);
            rows.push({
                id: raw.id,
                entityId: raw.cityentity_id,
                name: meta.name || building.name || raw.cityentity_id,
                era: building.eraName || era,
                type: meta.type || building.type || 'unknown',
                width: Number(footprint.xsize) || 0,
                length: Number(footprint.ysize) || 0,
                area,
                x: Number(raw.x) || 0,
                y: Number(raw.y) || 0,
                population: Number(building.population) || 0,
                happiness: Number(building.happiness) || 0,
                rating: Number(building.rating?.totalScore) || 0,
                production: values
            });
        }

        PlacedBuildings.Data = rows;
        const availableEras = new Set(rows.map(row => String(row.era)));
        const availableTypes = new Set(rows.map(row => String(row.type)));
        PlacedBuildings.State.eras = new Set([...PlacedBuildings.State.eras].filter(value => availableEras.has(value)));
        PlacedBuildings.State.types = new Set([...PlacedBuildings.State.types].filter(value => availableTypes.has(value)));
    },

    ReadProduction: (building) => {
        const amount = (category) => {
            try {
                const result = Productions.getBuildingProductionByCategory(false, building, category);
                if (category === 'goods') return Object.values(result?.eras || {}).reduce((sum, value) => sum + (Number(value) || 0), 0);
                return Number(result?.amount) || 0;
            } catch (error) {
                return 0;
            }
        };

        let guildGoods = 0;
        try {
            const result = CityBuildings.getBuildingGuildGoodsByEra(false, building, true);
            guildGoods = Object.values(result?.eras || {}).reduce((sum, value) => sum + (Number(value) || 0), 0);
        } catch (error) {}

        return {
            fp: amount('strategy_points'),
            coins: amount('money'),
            supplies: amount('supplies'),
            goods: amount('goods'),
            guildGoods,
            units: amount('units'),
            specialGoods: amount('special_goods'),
            medals: amount('medals'),
            diamonds: amount('premium')
        };
    },

    GetOptions: (values) => [...new Set(values.map(value => String(value)))].sort((a, b) => a.localeCompare(b)),

    CheckList: (id, values, selected) => {
        if (!values.length) return `<span class="pb-empty-filter">—</span>`;
        return values.map((value, index) => {
            const safe = HTML.escapeHtml(value);
            const checked = selected.has(value) ? ' checked' : '';
            return `<label class="pb-check"><input type="checkbox" class="${id}" value="${safe}"${checked}><span>${safe}</span></label>`;
        }).join('');
    },

    Render: () => {
        const t = key => PlacedBuildings.t(key);
        const eras = PlacedBuildings.GetOptions(PlacedBuildings.Data.map(row => row.era));
        const types = PlacedBuildings.GetOptions(PlacedBuildings.Data.map(row => row.type));
        const productionOptions = [
            ['fp', t('fp')], ['coins', t('coins')], ['supplies', t('supplies')], ['goods', t('goods')],
            ['guildGoods', t('guildGoods')], ['units', t('units')], ['specialGoods', t('specialGoods')],
            ['medals', t('medals')], ['diamonds', t('diamonds')]
        ];
        const selected = PlacedBuildings.State;

        const html = `
            <div class="pb-wrap">
                <div class="pb-toolbar">
                    <input id="pb-search" type="search" value="${HTML.escapeHtml(selected.query)}" placeholder="${HTML.escapeHtml(t('search'))}" autocomplete="off">
                    <button id="pb-refresh" class="btn">${HTML.escapeHtml(t('refresh'))}</button>
                </div>
                <div class="pb-filters">
                    <details class="pb-filter-box"><summary>${HTML.escapeHtml(t('age'))} (${eras.length})</summary><div id="pb-era-filters" class="pb-check-list">${PlacedBuildings.CheckList('pb-era-filter', eras, selected.eras)}</div></details>
                    <details class="pb-filter-box"><summary>${HTML.escapeHtml(t('types'))} (${types.length})</summary><div id="pb-type-filters" class="pb-check-list">${PlacedBuildings.CheckList('pb-type-filter', types, selected.types)}</div></details>
                    <details class="pb-filter-box"><summary>${HTML.escapeHtml(t('production'))} (${productionOptions.length})</summary><div id="pb-production-filters" class="pb-check-list">${productionOptions.map(([key, label]) => `<label class="pb-check"><input type="checkbox" class="pb-production-filter" value="${key}"${selected.productions.has(key) ? ' checked' : ''}><span>${HTML.escapeHtml(label)}</span></label>`).join('')}</div></details>
                    <label class="pb-select"><span>${HTML.escapeHtml(t('match'))}</span><select id="pb-production-mode"><option value="any"${selected.productionMode === 'any' ? ' selected' : ''}>${HTML.escapeHtml(t('any'))}</option><option value="all"${selected.productionMode === 'all' ? ' selected' : ''}>${HTML.escapeHtml(t('all'))}</option></select></label>
                    <label class="pb-select"><span>${HTML.escapeHtml(t('sort'))}</span><select id="pb-sort">
                        ${[['name',t('name')],['era',t('era')],['type',t('type')],['area',t('tiles')],['fp',t('fp')],['coins',t('coins')],['supplies',t('supplies')],['goods',t('goods')],['guildGoods',t('guildGoods')],['units',t('units')],['specialGoods',t('specialGoods')],['medals',t('medals')],['diamonds',t('diamonds')],['population',t('population')],['happiness',t('happiness')],['rating',t('rating')]].map(([value,label]) => `<option value="${value}"${selected.sort === value ? ' selected' : ''}>${HTML.escapeHtml(label)}</option>`).join('')}
                    </select></label>
                    <label class="pb-select"><span>${HTML.escapeHtml(t('direction'))}</span><select id="pb-direction"><option value="asc"${selected.direction === 'asc' ? ' selected' : ''}>${HTML.escapeHtml(t('asc'))}</option><option value="desc"${selected.direction === 'desc' ? ' selected' : ''}>${HTML.escapeHtml(t('desc'))}</option></select></label>
                    <label class="pb-select"><span>${HTML.escapeHtml(t('group'))}</span><select id="pb-group"><option value="none"${selected.group === 'none' ? ' selected' : ''}>${HTML.escapeHtml(t('none'))}</option><option value="era"${selected.group === 'era' ? ' selected' : ''}>${HTML.escapeHtml(t('age'))}</option><option value="type"${selected.group === 'type' ? ' selected' : ''}>${HTML.escapeHtml(t('types'))}</option></select></label>
                </div>
                <div id="pb-summary" class="pb-summary"></div>
                <div class="pb-table-wrap"><table class="foe-table pb-table"><thead><tr>
                    <th>${HTML.escapeHtml(t('name'))}</th><th>${HTML.escapeHtml(t('era'))}</th><th>${HTML.escapeHtml(t('type'))}</th>
                    <th>${HTML.escapeHtml(t('footprint'))}</th><th>${HTML.escapeHtml(t('tiles'))}</th><th>${HTML.escapeHtml(t('fp'))}</th>
                    <th>${HTML.escapeHtml(t('coins'))}</th><th>${HTML.escapeHtml(t('supplies'))}</th><th>${HTML.escapeHtml(t('goods'))}</th>
                    <th>${HTML.escapeHtml(t('guildGoods'))}</th><th>${HTML.escapeHtml(t('units'))}</th><th>${HTML.escapeHtml(t('specialGoods'))}</th>
                    <th>${HTML.escapeHtml(t('medals'))}</th><th>${HTML.escapeHtml(t('diamonds'))}</th><th>${HTML.escapeHtml(t('population'))}</th>
                    <th>${HTML.escapeHtml(t('happiness'))}</th><th>${HTML.escapeHtml(t('rating'))}</th><th>${HTML.escapeHtml(t('position'))}</th>
                </tr></thead><tbody id="pb-table-body"></tbody></table></div>
                <div class="pb-note">${HTML.escapeHtml(t('productionNote'))}</div>
            </div>`;

        $('#placedbuildingsBody').html(html);
        PlacedBuildings.BindEvents();
        PlacedBuildings.RenderTable();
    },

    BindEvents: () => {
        const $body = $('#placedbuildingsBody');
        $body.off('.placedBuildings');

        $body.on('input.placedBuildings', '#pb-search', function () {
            PlacedBuildings.State.query = String($(this).val() || '');
            PlacedBuildings.RenderTable();
        });

        $body.on('change.placedBuildings', '.pb-era-filter', function () {
            PlacedBuildings.State.eras = new Set($body.find('.pb-era-filter:checked').map(function () { return this.value; }).get());
            PlacedBuildings.RenderTable();
        });

        $body.on('change.placedBuildings', '.pb-type-filter', function () {
            PlacedBuildings.State.types = new Set($body.find('.pb-type-filter:checked').map(function () { return this.value; }).get());
            PlacedBuildings.RenderTable();
        });

        $body.on('change.placedBuildings', '.pb-production-filter', function () {
            PlacedBuildings.State.productions = new Set($body.find('.pb-production-filter:checked').map(function () { return this.value; }).get());
            PlacedBuildings.RenderTable();
        });

        $body.on('change.placedBuildings', '#pb-production-mode', function () {
            PlacedBuildings.State.productionMode = this.value;
            PlacedBuildings.RenderTable();
        });
        $body.on('change.placedBuildings', '#pb-sort', function () {
            PlacedBuildings.State.sort = this.value;
            PlacedBuildings.RenderTable();
        });
        $body.on('change.placedBuildings', '#pb-direction', function () {
            PlacedBuildings.State.direction = this.value;
            PlacedBuildings.RenderTable();
        });
        $body.on('change.placedBuildings', '#pb-group', function () {
            PlacedBuildings.State.group = this.value;
            PlacedBuildings.RenderTable();
        });
        $body.on('click.placedBuildings', '#pb-refresh', function () {
            PlacedBuildings.ReadData();
            PlacedBuildings.Render();
        });
        $body.on('click.placedBuildings', '.pb-locate', function () {
            const id = $(this).attr('data-building-id');
            const row = PlacedBuildings.Data.find(item => String(item.id) === String(id));
            if (!row) return;
            if (typeof BuildingMarker === 'undefined' || typeof BuildingMarker.show !== 'function') {
                HTML.ShowToastMsg({head: PlacedBuildings.t('title'), text: PlacedBuildings.t('locateUnavailable'), type: 'info', hideAfter: 3500});
                return;
            }
            BuildingMarker.show([row.id]);
        });
    },

    FilterData: () => {
        const state = PlacedBuildings.State;
        const query = state.query.trim().toLowerCase();
        const selectedProductions = [...state.productions];

        let rows = PlacedBuildings.Data.filter(row => {
            if (query && ![row.name, row.era, row.type, row.entityId, row.id].some(value => String(value ?? '').toLowerCase().includes(query))) return false;
            if (state.eras.size && !state.eras.has(String(row.era))) return false;
            if (state.types.size && !state.types.has(String(row.type))) return false;

            if (selectedProductions.length) {
                const matches = selectedProductions.map(key => Number(row.production[key]) > 0);
                if (state.productionMode === 'all' && !matches.every(Boolean)) return false;
                if (state.productionMode !== 'all' && !matches.some(Boolean)) return false;
            }
            return true;
        });

        const key = state.sort;
        rows.sort((a, b) => {
            // Keep grouped rows contiguous even when sorting by another column.
            if (state.group !== 'none') {
                const groupComparison = String(a[state.group] ?? '').localeCompare(String(b[state.group] ?? ''), undefined, {numeric: true, sensitivity: 'base'});
                if (groupComparison !== 0) return groupComparison;
            }

            let comparison;
            const av = key === 'name' ? a.name : key === 'era' ? a.era : key === 'type' ? a.type : key === 'area' ? a.area :
                key === 'population' ? a.population : key === 'happiness' ? a.happiness : key === 'rating' ? a.rating : a.production[key];
            const bv = key === 'name' ? b.name : key === 'era' ? b.era : key === 'type' ? b.type : key === 'area' ? b.area :
                key === 'population' ? b.population : key === 'happiness' ? b.happiness : key === 'rating' ? b.rating : b.production[key];
            if (typeof av === 'number' && typeof bv === 'number') comparison = av - bv;
            else comparison = String(av ?? '').localeCompare(String(bv ?? ''), undefined, {numeric: true, sensitivity: 'base'});
            return state.direction === 'desc' ? -comparison : comparison;
        });

        return rows;
    },

    FormatNumber: (value) => {
        const number = Number(value) || 0;
        const language = String(localStorage.getItem('user-language') || 'en').replace('_', '-');
        return number.toLocaleString(language, {maximumFractionDigits: 2});
    },

    RenderTable: () => {
        const rows = PlacedBuildings.FilterData();
        const t = key => PlacedBuildings.t(key);
        const tiles = rows.reduce((sum, row) => sum + row.area, 0);
        $('#pb-summary').text(`${rows.length} / ${PlacedBuildings.Data.length} ${t('buildings')} · ${PlacedBuildings.FormatNumber(tiles)} ${t('occupied')}`);

        if (!rows.length) {
            $('#pb-table-body').html(`<tr><td colspan="18" class="pb-no-results">${HTML.escapeHtml(PlacedBuildings.Data.length ? t('noResults') : t('noData'))}</td></tr>`);
            return;
        }

        const columns = [
            row => `<strong>${HTML.escapeHtml(row.name)}</strong><small class="pb-entity-id">${HTML.escapeHtml(String(row.entityId))}</small>`,
            row => HTML.escapeHtml(row.era),
            row => HTML.escapeHtml(row.type),
            row => `${row.width} × ${row.length}`,
            row => PlacedBuildings.FormatNumber(row.area),
            row => PlacedBuildings.FormatNumber(row.production.fp),
            row => PlacedBuildings.FormatNumber(row.production.coins),
            row => PlacedBuildings.FormatNumber(row.production.supplies),
            row => PlacedBuildings.FormatNumber(row.production.goods),
            row => PlacedBuildings.FormatNumber(row.production.guildGoods),
            row => PlacedBuildings.FormatNumber(row.production.units),
            row => PlacedBuildings.FormatNumber(row.production.specialGoods),
            row => PlacedBuildings.FormatNumber(row.production.medals),
            row => PlacedBuildings.FormatNumber(row.production.diamonds),
            row => PlacedBuildings.FormatNumber(row.population),
            row => PlacedBuildings.FormatNumber(row.happiness),
            row => PlacedBuildings.FormatNumber(row.rating),
            row => `${row.x}, ${row.y}`
        ];
        const groupKey = PlacedBuildings.State.group;
        const groupTitle = groupKey === 'era' ? t('era') : t('type');
        let currentGroup = null;
        const html = [];

        for (const row of rows) {
            if (groupKey !== 'none') {
                const value = String(row[groupKey]);
                if (value !== currentGroup) {
                    currentGroup = value;
                    html.push(`<tr class="pb-group-row"><td colspan="18">${HTML.escapeHtml(groupTitle)}: ${HTML.escapeHtml(value)}</td></tr>`);
                }
            }

            html.push(`<tr>
                ${columns.slice(0, 17).map(render => `<td>${render(row)}</td>`).join('')}
                <td>${row.x}, ${row.y}<br><button type="button" class="btn pb-locate" data-building-id="${HTML.escapeHtml(String(row.id))}">${HTML.escapeHtml(t('locate'))}</button></td>
            </tr>`);
        }

        $('#pb-table-body').html(html.join(''));
    }
};
